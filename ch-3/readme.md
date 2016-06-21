Storage Option In Android
===================


>Android provides the various storage option to save the offline data depending upon the user specific needs, such as whether the data should be private to your application or accessible to other applications (and the user) and how much space your data requires.

**Storage options**
 1. Shared Preferences (Store private primitive data in key-value pairs)
 2. Internal Storage (Store private data on the device memory)
 3. External Storage(Store public data on the shared external storage)
 4. SQLite Databases(Store structured data in a private database)
 5. Network Connection(Store data on the web with your own network server)
  

#### **SharedPreference**
> The SharedPreferences class provides a general framework that allows you to save and retrieve persistent key-value pairs of primitive data types. You can use SharedPreferences to save any primitive data: booleans, floats, ints, longs, and strings. This data will persist across user sessions (even if your application is killed).


    //Initializing the SharedPreferences    SharedPreferencespreferences=PreferenceManager.getDefaultSharedPreferences(ctx);

> Saving the data in the preferences, in the below function key is the name of value and value is the data need to be stored
 

     public void setPreferences(String key, String value) {
        preferences.edit().putString(key, value).commit();
        return this;
    }

> Retrieving the data from the preferences, find the data with the key
 

      public String getPreferences(String key) {
            return preferences.getString(key, "");
        }
finally to remove or clear the preferences

     // clear all saved preferences
     public boolean clearPreferences() {
            return preferences.edit().clear().commit();
        }
      // to clear preference by key
     public DbPreferences clearPreference(String Key){
        preferences.edit().remove(Key).apply();
        return this;
        }

----------


Sqlite DataBase
-------------

> Android provides full support for SQLite databases. Any databases you create will be accessible by name to any class in the application, but not outside the application. 
> Lets have a simple Application that saves contacts and reads contacts from the database 

**First create the Model for contact **
*Contact.java*

    package com.yuvi.contact;
    
    /**
     * Created by yubraj on 6/17/16.
     */
    
    /**
     * This is the model of the contact to hold the value of the contact
     * this will hold the id name and email
     *
     */
    public class Contacts {
        public String id, name, email;
        public Contacts(String id, String name, String email){
            this.id = id;
            this.name = name;
            this.email = email;
        }
        public Contacts(String name, String email){
            this.id = id;
            this.name = name;
            this.email = email;
        }
    
    }

Now create the Databasefile, please read through the line all the code are explained in the comment. 
*DataBaseHandler.java*

    package com.yuvi.contact;
    
    import android.content.ContentValues;
    import android.content.Context;
    import android.database.Cursor;
    import android.database.sqlite.SQLiteDatabase;
    import android.database.sqlite.SQLiteOpenHelper;
    import android.util.Log;
    
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * Created by yubraj on 6/17/16.
     */
    public class DataBaseHandler extends SQLiteOpenHelper {
        static int DATABASE_VERSION = 1;
        static String DATABASE_NAME = "contact";
    
        public DataBaseHandler(Context context) {
            super(context, DATABASE_NAME, null, DATABASE_VERSION);
        }
    
        @Override
        public void onCreate(SQLiteDatabase sqLiteDatabase) {
            // create the table
            // this sytax will create the table contact
            // table has the fields id, name, email
            // id is autoincrement that means you dont need to worry to put the id
            // it will increase the id value by one itself
            // primary means duplicate id is ignored
            String SQL = "CREATE TABLE contact (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, email TEXT)";
            sqLiteDatabase.execSQL(SQL);
        }
    
        @Override
        public void onUpgrade(SQLiteDatabase sqLiteDatabase, int oldDatabaseVersion, int dataBaseVersion) {
            // delete the old table
            // create the new table
            sqLiteDatabase.delete("contact", null, null);
            onCreate(sqLiteDatabase);
        }
        // CRUD operation
        // create read update delete
    
        public Contacts getContact(String id) {
            SQLiteDatabase db = this.getReadableDatabase();
            Cursor cursor = db.query("contact", null, "id = ?", new String[]{id}, null, null, null);
            String ids = cursor.getString(0);
            String name = cursor.getString(1);
            String email = cursor.getString(2);
            Contacts contacts = new Contacts(ids, name, email);
            db.close();
            return contacts;
    
        }
    
        public List<Contacts> getAllContact() {
            SQLiteDatabase db = this.getReadableDatabase();
            List<Contacts> contactList = new ArrayList<>();
            Cursor cursor = db.query("contact", null, null, null, null, null, null);
            while (cursor.moveToNext()) {
                String ids = cursor.getString(0);
                String name = cursor.getString(1);
                String email = cursor.getString(2);
                Contacts contacts = new Contacts(ids, name, email);
                contactList.add(contacts);
            }
            db.close();
            return contactList;
    
        }
    
        public void saveContact(Contacts contacts) {
            // we need to save the data
            // hence initialize the writable type of the database
            SQLiteDatabase db = this.getWritableDatabase();
            // content values is needed to save the data
            // all the data we are going to put in the database is first put in the contentvalues
            // then we put the content values in the table
            ContentValues cv = new ContentValues();
            cv.put("name", contacts.name);
            cv.put("email", contacts.email);
            // finally insert the content values in the table
            long result = db.insert("contact", null, cv);
            Log.d("DataBaseHandler", " result = " + result);
            db.close();
        }
    
        public void deleteContact(String id) {
            SQLiteDatabase db = this.getWritableDatabase();
            db.delete("contact", "id = ?", new String[]{id});
            db.close();
        }
    }

> Now we are almost ready to save or retrieve the data. Lets build the application which will load the two pages read contact and save contact in the form of fragment.

first as other application create the main activity xml file
*activity_main.xml*

  

     <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context="com.yuvi.contact.MainActivity">
    
        <android.support.design.widget.TabLayout
            android:id="@+id/tab"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@color/colorPrimary" />
    
        <android.support.v4.view.ViewPager
            android:id="@+id/pager"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>


We need two more layout file for read contacts and save contacts so lets create two file respectively

*fragment_read_contact.xml*

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    
    
        <ListView
            android:id="@+id/lv_contacts"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:divider="#212121"
            android:dividerHeight="1dp" />
    
    </LinearLayout>

*fragment_save_contact.xml*

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="8dp"
        android:orientation="vertical">
    
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:minHeight="48dp"
            android:orientation="horizontal">
    
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textColor="#212121"
                android:textSize="18sp"
                android:textStyle="bold"
                android:text="Name" />
    
            <EditText
                android:id="@+id/edt_name"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginLeft="16dp"
                android:hint="Name" />
    
        </LinearLayout>
    
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:minHeight="48dp"
            android:orientation="horizontal">
            <TextView
                android:layout_width="wrap_content"
                android:textColor="#212121"
                android:textSize="18sp"
                android:textStyle="bold"
                android:layout_height="wrap_content"
                android:text="Email" />
    
            <EditText
                android:id="@+id/edt_email"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:layout_marginLeft="16dp"
                android:hint="Email" />
    
        </LinearLayout>
    
        <Button
            android:id="@+id/btn_save"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:text="Save Contact" />
    
    </LinearLayout>

and lastly we need row layout file needed in read contacts for displaying the individual contacts in the database.

*row_contact.xml*

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:minHeight="48dp"
        android:orientation="vertical"
        android:padding="8dp">
    
        <TextView
            android:id="@+id/tv_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Yubaraj poudel"
            android:textColor="#212121"
            android:textSize="16sp" />
    
        <TextView
            android:id="@+id/tv_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="8dp"
            android:text="yubarajpoudel708@gmail.com" />
    
    </LinearLayout>

> Now we are almost ready to code for saving and reading. Dont worry the hardest part and most important part is already completed now. If you have understood the above code then scroll down otherwise i recommend you to pause here go through the code once.

*MainActivity.java*

    package com.yuvi.contact;
    
    import android.os.Bundle;
    import android.support.design.widget.TabLayout;
    import android.support.v4.app.Fragment;
    import android.support.v4.app.FragmentStatePagerAdapter;
    import android.support.v4.view.ViewPager;
    import android.support.v7.app.AppCompatActivity;
    
    public class MainActivity extends AppCompatActivity {
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            // we have viewpager and tablayout
            // initialize the view in the activity
    
            TabLayout tab = (TabLayout) findViewById(R.id.tab);
            ViewPager pager = (ViewPager) findViewById(R.id.pager);
            // initialize the adapter
    
            FragmentAdapter adapter = new FragmentAdapter();
            pager.setAdapter(adapter);
            tab.setupWithViewPager(pager);
    
            //pager.set
            // pager can load many fragment in it
            // so it need the adapter to publish the fragment in the viewpager
    
    
        }
    
        // this is the adapter needed to manage the multiple fragment in the viewpager
        class FragmentAdapter extends FragmentStatePagerAdapter {
            String[] titles = {"Save Contact", " Read Contact"};
    
            public FragmentAdapter() {
                super(getSupportFragmentManager());
            }
    
            @Override
            public int getCount() {
                // getCount means how many fragment will be loaded in the adapter
                // ??? here i prefer to give the length as the title length
                return titles.length;
            }
    
            @Override
            public Fragment getItem(int position) {
                // this function returns the fragment need to be display at the given position
                // for example at first position is 0, when we scroll the position increases upto
                // the value returned by getCount function.
                if (position == 0) {
                    return new SaveContactFragment();
                } else if (position == 1) {
                    return new ReadContactFragment();
                }
    
                return null;
            }
    
    
            @Override
            public CharSequence getPageTitle(int position) {
                return titles[position];
    
                // titles[0] at position 0 is saveContact
                // titles[1] at position 1 is readContact
            }
        }
    }

Now to do two operation read and save we are using fragments here so we need two fragment.

*ReadContactFragment.java*

    package com.yuvi.contact;
    
    import android.os.Bundle;
    import android.support.annotation.Nullable;
    import android.support.v4.app.Fragment;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.ArrayAdapter;
    import android.widget.ListView;
    import android.widget.TextView;
    
    import java.util.List;
    
    /**
     * Created by yubraj on 6/18/16.
     */
    public class ReadContactFragment extends Fragment {
        @Nullable
        @Override
        public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View view = inflater.inflate(R.layout.fragment_read_contact, container, false);
            return view;
    
        }
    
        @Override
        public void onActivityCreated(@Nullable Bundle savedInstanceState) {
            super.onActivityCreated(savedInstanceState);
            List<Contacts> contactsList = new DataBaseHandler(getActivity()).getAllContact();
            ListView listView = (ListView) getView().findViewById(R.id.lv_contacts);
            listView.setAdapter(new ArrayAdapter<Contacts>(getActivity(), R.layout.row_contact, contactsList) {
                @Override
                public View getView(int position, View convertView, ViewGroup parent) {
                    if (convertView == null) {
                        convertView = LayoutInflater.from(getActivity()).inflate(R.layout.row_contact, null);
                    }
                    ((TextView) convertView.findViewById(R.id.tv_name)).setText(getItem(position).name);
                    ((TextView) convertView.findViewById(R.id.tv_email)).setText(getItem(position).email);
                    return convertView;
                }
            });
        }
    }

*SaveContactFragment.java*

    package com.yuvi.contact;
    
    import android.os.Bundle;
    import android.support.annotation.Nullable;
    import android.support.v4.app.Fragment;
    import android.util.Log;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.EditText;
    import android.widget.Toast;
    
    import java.util.List;
    
    /**
     * Created by yubraj on 6/18/16.
     */
    public class SaveContactFragment extends Fragment {
        DataBaseHandler db;
    
        @Nullable
        // the fragment need the view to be populated
        // so give the view
        // we have the view in the xml file hence to convert the xml to view
        // we have to inflate the xml
        @Override
        public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View view = LayoutInflater.from(getActivity()).inflate(R.layout.fragment_save_contact, container, false);
            return view;
        }
    
        @Override
        public void onActivityCreated(@Nullable Bundle savedInstanceState) {
            super.onActivityCreated(savedInstanceState);
            // get the email and name from the user
            // when the save button is clicked then save the data in the dataabase
            // algorithm
            // when the button is clicked then take the data from the fields and save it to the database
    
            // so lets begin
            db = new DataBaseHandler(getActivity());
    
            getView().findViewById(R.id.btn_save).setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    // now take the data
                    String name = "", email = "";
                    name = ((EditText) getView().findViewById(R.id.edt_name)).getText().toString();
                    email = ((EditText) getView().findViewById(R.id.edt_email)).getText().toString();
                    boolean dataIsValid = true;
                    if (name.isEmpty()) {
                        toast("Name is empty");
                        dataIsValid = false;
                    }
                    if (email.isEmpty()) {
                        toast("email is empty");
                        dataIsValid = false;
                    }
                    if (dataIsValid) {
                        // then save the data in the database
                        // create the object of the database to save the details in the table
                        // we pass the data in from the form of class
                        // contacts object is created
                        Contacts contacts = new Contacts(name, email);
                        // create the object of the database
    
                        db.saveContact(contacts);
                        // data is saved in the database
                    }
                }
            });
    
            List<Contacts> contactsList = db.getAllContact();
            for (Contacts contacts : contactsList) {
                Log.d("SaveContact", " saved Name = " + contacts.name);
            }
        }
    
        private void toast(String s) {
            Toast.makeText(getActivity(), s, Toast.LENGTH_SHORT).show();
        }
    }

> Thats all !! all the codes are tested so even if you didnot understand you can copy the code and use it to see the result. Please go through the comments to know more about the codes.

**OutPut**

![enter image description here](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-3/read_contact.png)

![enter image description here](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-3/save_contact.png)

![enter image description here](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-3/save_contact_filled.png)

> If you got these output then its done.. Time for coffee.