---
title: 用Fragments创建动态UI
layout: post
category: Android
---

fragments可以看作一个嵌套的(nested)activity，可以定义自己的layout，管理自己的lifecycle

1.  创建fragments
-----------------
    
    ***创建一个Fragment类***

        import android.os.Bundle;
        import android.support.v4.app.Fragment;
        import android.view.LayoutInflater;
        import android.view.ViewGroup;

        public class ArticleFragment extends Fragment {
            @Override
            public View onCreateView(LayoutInflater inflater, ViewGroup container,
                Bundle savedInstanceState) {
                // Inflate the layout for this fragment
                return inflater.inflate(R.layout.article_view, container, false);
            }
        }

    ***添加Fragment到Activity***

    用xml

    `res/layout-large/news_articles.xml:`

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:orientation="horizontal"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent">

            <fragment android:name="com.example.android.fragments.HeadlinesFragment"
                      android:id="@+id/headlines_fragment"
                      android:layout_weight="1"
                      android:layout_width="0dp"
                      android:layout_height="match_parent" />

            <fragment android:name="com.example.android.fragments.ArticleFragment"
                      android:id="@+id/article_fragment"
                      android:layout_weight="2"
                      android:layout_width="0dp"
                      android:layout_height="match_parent" />

        </LinearLayout>


    ***activity调用layout***

        import android.os.Bundle;
        import android.support.v4.app.FragmentActivity;

        // FragmentActivity是android support library v4 中的特别类，api 11 后可以直接用Activity类
        public class MainActivity extends FragmentActivity {
            @Override
            public void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.news_articles);
            }
        }

    如果用v7 appcompat library，则用ActionBarActivity类，它是FragmentActivity的子类。

    以上直接在layout中定义Fragments，这种方法不能在运行时(runtime)移出fragments。（理论上感觉是可以，就是移除xml树的子节点，不过文档说不可以）


2.  构建灵活的UI
----------------

    在大屏幕显示两个fragment，在小屏幕显示一个fragment

    ***运行时添加fragment到activity***

    res/layout/news_articles.xml:
        <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@+id/fragment_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

    ***添加fragment***

        import android.os.Bundle;
        import android.support.v4.app.FragmentActivity;

        public class MainActivity extends FragmentActivity {
            @Override
            public void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.news_articles);

                // Check that the activity is using the layout version with
                // the fragment_container FrameLayout
                if (findViewById(R.id.fragment_container) != null) {

                    // However, if we're being restored from a previous state,
                    // then we don't need to do anything and should return or else
                    // we could end up with overlapping fragments.
                    if (savedInstanceState != null) {
                        return;
                    }

                    // Create a new Fragment to be placed in the activity layout
                    HeadlinesFragment firstFragment = new HeadlinesFragment();
                    
                    // In case this activity was started with special instructions from an
                    // Intent, pass the Intent's extras to the fragment as arguments
                    firstFragment.setArguments(getIntent().getExtras());
                    
                    // Add the fragment to the 'fragment_container' FrameLayout
                    getSupportFragmentManager().beginTransaction()
                            .add(R.id.fragment_container, firstFragment).commit();
                }
            }
        }

    ***用第二个fragment替代以上的fragment***

        // Create fragment and give it an argument specifying the article it should show
        ArticleFragment newFragment = new ArticleFragment();
        Bundle args = new Bundle();
        args.putInt(ArticleFragment.ARG_POSITION, position);
        newFragment.setArguments(args);

        FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

        // Replace whatever is in the fragment_container view with this fragment,
        // and add the transaction to the back stack so the user can navigate back
        transaction.replace(R.id.fragment_container, newFragment);
        transaction.addToBackStack(null);

        // Commit the transaction
        transaction.commit();


3.  fragments间通信
-------------------

    ***定义侦听器(Callback接口)***

        public class HeadlinesFragment extends ListFragment {
            OnHeadlineSelectedListener mCallback;

            // Container Activity must implement this interface
            public interface OnHeadlineSelectedListener {
                public void onArticleSelected(int position);
            }

            @Override
            public void onAttach(Activity activity) {
                super.onAttach(activity);
                
                // This makes sure that the container activity has implemented
                // the callback interface. If not, it throws an exception
                try {
                    mCallback = (OnHeadlineSelectedListener) activity;
                } catch (ClassCastException e) {
                    throw new ClassCastException(activity.toString()
                            + " must implement OnHeadlineSelectedListener");
                }
            }
            
            ...
        }

    ***重写onListItemClick()方法***

        @Override
        public void onListItemClick(ListView l, View v, int position, long id) {
            // Send the event to the host activity
            mCallback.onArticleSelected(position);
        }


    ***实现Callback接口***

        public static class MainActivity extends Activity
                implements HeadlinesFragment.OnHeadlineSelectedListener{
            ...
            
            public void onArticleSelected(int position) {
                // The user selected the headline of an article from the HeadlinesFragment
                // Do something here to display that article
            }
        }

    ***发送信息到fragment***
    
        public static class MainActivity extends Activity
                implements HeadlinesFragment.OnHeadlineSelectedListener{
            ...

            public void onArticleSelected(int position) {
                // The user selected the headline of an article from the HeadlinesFragment
                // Do something here to display that article

                ArticleFragment articleFrag = (ArticleFragment)
                        getSupportFragmentManager().findFragmentById(R.id.article_fragment);

                if (articleFrag != null) {
                    // If article frag is available, we're in two-pane layout...

                    // Call a method in the ArticleFragment to update its content
                    articleFrag.updateArticleView(position);
                } else {
                    // Otherwise, we're in the one-pane layout and must swap frags...

                    // Create fragment and give it an argument for the selected article
                    ArticleFragment newFragment = new ArticleFragment();
                    Bundle args = new Bundle();
                    args.putInt(ArticleFragment.ARG_POSITION, position);
                    newFragment.setArguments(args);
                
                    FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

                    // Replace whatever is in the fragment_container view with this fragment,
                    // and add the transaction to the back stack so the user can navigate back
                    transaction.replace(R.id.fragment_container, newFragment);
                    transaction.addToBackStack(null);

                    // Commit the transaction
                    transaction.commit();
                }
            }
        }
