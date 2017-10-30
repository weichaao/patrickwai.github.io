---
title: 构建能分享内容的App
layout: post
category: Android
---

1.  分享简单的数据
------------------

    ###发送数据给其他的APPS###

    ***发送文本***

        Intent sendIntent = new Intent();
        sendIntent.setAction(Intent.ACTION_SEND);
        sendIntent.putExtra(Intent.EXTRA_TEXT, "This is my text to send.");
        sendIntent.setType("text/plain");
        startActivity(sendIntent);

    如果已有的app的过滤器(filter)匹配 ACTION_SEND 和 `MIME TYPE`，系统会启动它。
    如果有多个app匹配，系统会显示一个选择器


    我们也可以用Intent.createChooser()来始终显示chooser，优点:

    * Even if the user has previously selected a default action for this intent, the chooser will still be displayed.

    * If no applications match, Android displays a system message.

    * You can specify a title for the chooser dialog.

    ***更新的代码***

        Intent sendIntent = new Intent();
        sendIntent.setAction(Intent.ACTION_SEND);
        sendIntent.putExtra(Intent.EXTRA_TEXT, "This is my text to send.");
        sendIntent.setType("text/plain");
        startActivity(Intent.createChooser(sendIntent, getResources().getText(R.string.send_to)));

    ***发送二进制数据***

        Intent shareIntent = new Intent();
        shareIntent.setAction(Intent.ACTION_SEND);
        shareIntent.putExtra(Intent.EXTRA_STREAM, uriToImage);
        shareIntent.setType("image/jpeg");
        startActivity(Intent.createChooser(shareIntent, getResources().getText(R.string.send_to)));

    ***发送多段内容***

    发送多段内容用动作`ACTION_SEND_MULTIPLE`

        ArrayList<Uri> imageUris = new ArrayList<Uri>();
        imageUris.add(imageUri1); // Add your image URIs here
        imageUris.add(imageUri2);

        Intent shareIntent = new Intent();
        shareIntent.setAction(Intent.ACTION_SEND_MULTIPLE);
        shareIntent.putParcelableArrayListExtra(Intent.EXTRA_STREAM, imageUris);
        shareIntent.setType("image/*");
        startActivity(Intent.createChooser(shareIntent, "Share images to.."));

    如果发送的是多种类型的数据，可以用`*/*`，叫做泛型。
    Intent的其他Action有：EXTRA_EMAIL, EXTRA_CC, EXTRA_BCC, EXTRA_SUBJECT

    ###接收简单的数据###
    
    ***更新manifest***

        <activity android:name=".ui.MyActivity" >
            <intent-filter>
                <action android:name="android.intent.action.SEND" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="image/*" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.SEND" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.SEND_MULTIPLE" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="image/*" />
            </intent-filter>
        </activity>

    ***处理发送的(incoming)数据***

        void onCreate (Bundle savedInstanceState) {
            ...
            // Get intent, action and MIME type
            Intent intent = getIntent();
            String action = intent.getAction();
            String type = intent.getType();

            if (Intent.ACTION_SEND.equals(action) && type != null) {
                if ("text/plain".equals(type)) {
                    handleSendText(intent); // Handle text being sent
                } else if (type.startsWith("image/")) {
                    handleSendImage(intent); // Handle single image being sent
                }
            } else if (Intent.ACTION_SEND_MULTIPLE.equals(action) && type != null) {
                if (type.startsWith("image/")) {
                    handleSendMultipleImages(intent); // Handle multiple images being sent
                }
            } else {
                // Handle other intents, such as being started from the home screen
            }
            ...
        }

        void handleSendText(Intent intent) {
            String sharedText = intent.getStringExtra(Intent.EXTRA_TEXT);
            if (sharedText != null) {
                // Update UI to reflect text being shared
            }
        }

        void handleSendImage(Intent intent) {
            Uri imageUri = (Uri) intent.getParcelableExtra(Intent.EXTRA_STREAM);
            if (imageUri != null) {
                // Update UI to reflect image being shared
            }
        }

        void handleSendMultipleImages(Intent intent) {
            ArrayList<Uri> imageUris = intent.getParcelableArrayListExtra(Intent.EXTRA_STREAM);
            if (imageUris != null) {
                // Update UI to reflect multiple images being shared
            }
        }

    ###添加分享动作###
    
    用ActionProvider的子类SharedActionProvider类

    ***更新menu***

        <menu xmlns:android="http://schemas.android.com/apk/res/android">
            <item
                    android:id="@+id/menu_item_share"
                    android:showAsAction="ifRoom"
                    android:title="Share"
                    android:actionProviderClass=
                        "android.widget.ShareActionProvider" />
            ...
        </menu>

    以上表示委托(delegate)item的呈现和功能给ShareActionProvider。还有我们需要告诉ShareActionProvider what we would like to share.

    ***设置Intent***

        private ShareActionProvider mShareActionProvider;
        ...

        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            // Inflate menu resource file.
            getMenuInflater().inflate(R.menu.share_menu, menu);

            // Locate MenuItem with ShareActionProvider
            MenuItem item = menu.findItem(R.id.menu_item_share);

            // Fetch and store ShareActionProvider
            mShareActionProvider = (ShareActionProvider) item.getActionProvider();

            // Return true to display menu
            return true;
        }

        // Call to update the share intent
        private void setShareIntent(Intent shareIntent) {
            if (mShareActionProvider != null) {
                mShareActionProvider.setShareIntent(shareIntent);
            }
        }


2.  分享文件
------------

    TO BE UPDATED

3.  用NFC分享文件
-----------------

    TO BE UPDATED
