---
title: 保存数据
layout: post
category: Android
---

1.  保存键值对集
----------------

        getSharedPreferences()
        getPreferences()

        Context context = getActivity();
        SharedPreferences sharedPref = context.getSharedPreferences(
                getString(R.string.preference_file_key), Context.MODE_PRIVATE);

    用一个共享的preference

        SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);

    write to shared preferences

        SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
        SharedPreferences.Editor editor = sharedPref.edit();
        editor.putInt(getString(R.string.saved_high_score), newHighScore);
        editor.commit();

    Read from shared preferences

        SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
        int defaultValue = getResources().getInteger(R.string.saved_high_score_default);
        long highScore = sharedPref.getInt(getString(R.string.saved_high_score), defaultValue);

2.  保存文件
------------

    ***外部存储***

    获取权限，写外部存储和读外部存储

        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    对于内部存储，app不需要声明获取权限。app总是有权限读写它自己的内部存储(internal storage)

    ***内部存储***

    *   getFileDir()

    *   getCacheDir()

    创建文件

        File file = new File(context.getFileDir(), fileName)

    用openFileOutput()写内部存储

        String filename = "myfile";
        String string = "Hello world!";
        FileOutputStream outputStream;

        try {
          outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
          outputStream.write(string.getBytes());
          outputStream.close();
        } catch (Exception e) {
          e.printStackTrace();
        }

    用 createTempFile()写内部缓存文件

        public File getTempFile(Context context, String url) {
            File file;
            try {
                String fileName = Uri.parse(url).getLastPathSegment();
                file = File.createTempFile(fileName, null, context.getCacheDir());
            catch (IOException e) {
                // Error while creating file
            }
            return file;
        }

    写文件到外部存储(external storage)

    检测外部存储是否可写入

        /* Checks if external storage is available for read and write */
        public boolean isExternalStorageWritable() {
            String state = Environment.getExternalStorageState();
            if (Environment.MEDIA_MOUNTED.equals(state)) {
                return true;
            }
            return false;
        }

    检测外部存储是否可读

        /* Checks if external storage is available to at least read */
        public boolean isExternalStorageReadable() {
            String state = Environment.getExternalStorageState();
            if (Environment.MEDIA_MOUNTED.equals(state) ||
                Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
                return true;
            }
            return false;
        }

    外部存储对所有app是可见的

    *   公共文件

    *   私有文件

    用getExternalStoragePublicDirectory()获取公共文件夹

        public File getAlbumStorageDir(String albumName) {
            // Get the directory for the user's public pictures directory. 
            File file = new File(Environment.getExternalStoragePublicDirectory(
                    Environment.DIRECTORY_PICTURES), albumName);
            if (!file.mkdirs()) {
                Log.e(LOG_TAG, "Directory not created");
            }
            return file;
        }

    公共文件夹环境变量 DIRECTORY_MUSIC ， DIRECTORY_PICTURES。通过Environment获取

    用context.getExternalFilesDir()获取私有的外部存储文件夹

        public File getAlbumStorageDir(Context context, String albumName) {
            // Get the directory for the app's private pictures directory. 
            File file = new File(context.getExternalFilesDir(
                    Environment.DIRECTORY_PICTURES), albumName);
            if (!file.mkdirs()) {
                Log.e(LOG_TAG, "Directory not created");
            }
            return file;
        }

    删除文件
        myFile.delete()

        myContext.deleteFile(fileName)

    当删除app时，android系统会删除：

    *   所有的保存在内部存储的文件
    *   所有的保存在外部存储的，并且是用getExternalFilesDir()的文件

3.  保存数据在SQL数据库(用sqlite api)
-------------------------------------

    定义一个表结构

        public final class FeedReaderContract {
            // To prevent someone from accidentally instantiating the contract class,
            // give it an empty constructor.
            public FeedReaderContract() {}

            /* Inner class that defines the table contents */
            public static abstract class FeedEntry implements BaseColumns {
                public static final String TABLE_NAME = "entry";
                public static final String COLUMN_NAME_ENTRY_ID = "entryid";
                public static final String COLUMN_NAME_TITLE = "title";
                public static final String COLUMN_NAME_SUBTITLE = "subtitle";
                ...
            }
        }


    创建数据库

    sql语句

        private static final String TEXT_TYPE = " TEXT";
        private static final String COMMA_SEP = ",";
        private static final String SQL_CREATE_ENTRIES =
            "CREATE TABLE " + FeedEntry.TABLE_NAME + " (" +
            FeedEntry._ID + " INTEGER PRIMARY KEY," +
            FeedEntry.COLUMN_NAME_ENTRY_ID + TEXT_TYPE + COMMA_SEP +
            FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
            ... // Any other options for the CREATE command
            " )";

        private static final String SQL_DELETE_ENTRIES =
            "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;

    创建SQLiteOpenHelper

        public class FeedReaderDbHelper extends SQLiteOpenHelper {
            // If you change the database schema, you must increment the database version.
            public static final int DATABASE_VERSION = 1;
            public static final String DATABASE_NAME = "FeedReader.db";

            public FeedReaderDbHelper(Context context) {
                super(context, DATABASE_NAME, null, DATABASE_VERSION);
            }
            public void onCreate(SQLiteDatabase db) {
                db.execSQL(SQL_CREATE_ENTRIES);
            }
            public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
                // This database is only a cache for online data, so its upgrade policy is
                // to simply to discard the data and start over
                db.execSQL(SQL_DELETE_ENTRIES);
                onCreate(db);
            }
            public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
                onUpgrade(db, oldVersion, newVersion);
            }
        }

    实例化数据库helper

        FeedReaderDbHelper mDbHelper = new FeedReaderDbHelper(getContext());

    插入数据。传递一个ContentValues对象给db.insert()方法

        // Gets the data repository in write mode
        SQLiteDatabase db = mDbHelper.getWritableDatabase();

        // Create a new map of values, where column names are the keys
        ContentValues values = new ContentValues();
        values.put(FeedEntry.COLUMN_NAME_ENTRY_ID, id);
        values.put(FeedEntry.COLUMN_NAME_TITLE, title);
        values.put(FeedEntry.COLUMN_NAME_CONTENT, content);

        // Insert the new row, returning the primary key value of the new row
        long newRowId;
        newRowId = db.insert(
                 FeedEntry.TABLE_NAME,
                 FeedEntry.COLUMN_NAME_NULLABLE,
                 values);


    读取数据

        SQLiteDatabase db = mDbHelper.getReadableDatabase();

        // Define a projection that specifies which columns from the database
        // you will actually use after this query.
        String[] projection = {
            FeedEntry._ID,
            FeedEntry.COLUMN_NAME_TITLE,
            FeedEntry.COLUMN_NAME_UPDATED,
            ...
            };

        // How you want the results sorted in the resulting Cursor
        String sortOrder =
            FeedEntry.COLUMN_NAME_UPDATED + " DESC";

        Cursor c = db.query(
            FeedEntry.TABLE_NAME,                     // The table to query
            projection,                               // The columns to return
            selection,                                // The columns for the WHERE clause
            selectionArgs,                            // The values for the WHERE clause
            null,                                     // don't group the rows
            null,                                     // don't filter by row groups
            sortOrder                                 // The sort order
            );

    查看row

        cursor.moveToFirst();
        long itemId = cursor.getLong(
            cursor.getColumnIndexOrThrow(FeedEntry._ID)
        );

    删除

        // Define 'where' part of query.
        String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
        // Specify arguments in placeholder order.
        String[] selectionArgs = { String.valueOf(rowId) };
        // Issue SQL statement.
        db.delete(table_name, selection, selectionArgs);

        Update

        SQLiteDatabase db = mDbHelper.getReadableDatabase();

        // New value for one column
        ContentValues values = new ContentValues();
        values.put(FeedEntry.COLUMN_NAME_TITLE, title);

        // Which row to update, based on the ID
        String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
        String[] selectionArgs = { String.valueOf(rowId) };

        int count = db.update(
            FeedReaderDbHelper.FeedEntry.TABLE_NAME,
            values,
            selection,
            selectionArgs);

