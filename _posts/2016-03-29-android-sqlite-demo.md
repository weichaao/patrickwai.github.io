```java
package com.example.patrick.sqlitedemo;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.DatabaseErrorHandler;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MY";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 创建数据库
        SQLiteOpenHelper helper = new DatabaseHelper(this, "demo", null, 1);
        // 获取数据库实例
        final SQLiteDatabase database = helper.getWritableDatabase();

        findViewById(R.id.insert).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.i(TAG, "insert 1");
                insert(database, "person");
            }
        });

        findViewById(R.id.delete).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                delete(database, "person");
            }
        });

        findViewById(R.id.update).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                update(database, "person");
            }
        });

        findViewById(R.id.query).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                query(database, "person");
            }
        });

    }

    public void insert(SQLiteDatabase database, String table) {
        // 插入数据
        Log.i(TAG, "insert");
        ContentValues contentValues = new ContentValues();

        contentValues.put("first_name", "Bill");
        contentValues.put("last_name", "Gates");
        database.insert(table, null, contentValues);

        contentValues = new ContentValues();
        contentValues.put("first_name", "Steve");
        contentValues.put("last_name", "Jobs");
        database.insert(table, null, contentValues);
    }

    public void delete(SQLiteDatabase database, String table) {

        // 删除数据
        Log.i(TAG, "delete");
        database.delete(table, "first_name=?", new String[]{"Bill"});
    }


    public void update(SQLiteDatabase database, String table) {
        // 修改数据
        Log.i(TAG, "update");
        ContentValues contentValues;
        contentValues = new ContentValues();
        contentValues.put("first_name", "Bill");
        database.update("person", contentValues, "first_name=?", new String[]{"Steve"});
    }

    public void query(SQLiteDatabase database, String table) {

        // 查询数据
        Log.i(TAG, "query");
        Cursor cursor = database.query(true, table, null, null, null, null, null, null, null);

        while (cursor.moveToNext()) {
            Log.i(TAG, String.format(
                    "{person: {\"_id\": %d, \"first_name\": %s, \"last_name\": %s",
                    cursor.getInt(0), cursor.getString(1), cursor.getString(2)));
        }
    }

    public class DatabaseHelper extends SQLiteOpenHelper {
        private static final String DATABASE_NAME = "demo";
        private static final String TABLE_NAME_PERSON = "person";
        private static final String TABLE_PERSON_CREATION = "" +
                "CREATE TABLE " + TABLE_NAME_PERSON + " (" +
                "_id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                "first_name TEXT, " +
                "last_name TEXT);";

        private static final int VERSION = 2;

        public DatabaseHelper(Context context) {
            super(context, DATABASE_NAME, null, 1);
        }

        public DatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
            super(context, name, factory, version);
        }

        public DatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version, DatabaseErrorHandler errorHandler) {
            super(context, name, factory, version, errorHandler);
        }

        @Override
        public void onCreate(SQLiteDatabase db) {
            db.execSQL(TABLE_PERSON_CREATION);
        }

        @Override
        public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
            db.execSQL("alter table person drop constraint _id INTERGER PRIMARY KEY AUTOINCREMENT");
        }

        @Override
        public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
            super.onDowngrade(db, oldVersion, newVersion);
        }
    }
}
```
