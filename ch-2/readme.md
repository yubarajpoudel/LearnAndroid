ListView
===================


>Android ListView is a view which groups several items and display them in vertical scrollable list. The list items are automatically inserted to the list using an Adapter that pulls content from a source such as an array or database.

**Adapter**
>An adapter actually bridges between UI components and the data source that fill data into UI Component. Adapter holds the data and send the data to adapter view, the view can takes the data from adapter view and shows the data on different views like as spinner, list view, grid view etc.

#### **ListView Attributes**

 >| android:id                    | This is the ID which uniquely identifies the layout.                                                          |
|-------------------------------|---------------------------------------------------------------------------------------------------------------|
| android:divider               | This is drawable or color to draw between list items. .                                                       |
| android:dividerHeight         | This specifies height of the divider. This could be in px, dp, sp, in, or mm.                                 |
| android:entries               | Specifies the reference to an array resource that will populate the ListView.                                 |
| android:footerDividersEnabled | When set to false, the ListView will not draw the divider before each footer view. The default value is true. |
| android:headerDividersEnabled | When set to false, the ListView will not draw the divider after each header view. The default value is true.  |>
 
----------


Lets start make our first Simple Listview
-------------

Before you begin start programming, if you have not read carefully above article then pause here and study the above article first.

*list_activity.xml*

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <!-- listview is created and given the id lv. this will identify this layout from other divider is a sperator -->
        <ListView
            android:id="@+id/lv"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:divider="#212121"
            android:dividerHeight="1dp"/>
    
    </LinearLayout>
Next lets make the ListActivity class 

    *ListActivity.java*
    package com.yuvi.myapplication;
    
    import android.os.Bundle;
    import android.support.annotation.Nullable;
    import android.support.v7.app.AppCompatActivity;
    import android.widget.ArrayAdapter;
    import android.widget.ListView;
    
    /**
     * Created by yubraj on 6/12/16.
     */
    
        public class ListActivity extends AppCompatActivity {
            @Override
            protected void onCreate(@Nullable Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.list_activity);
                // data source:: what data should be published in the listview
                // adapter :: to populate the data in the listview
                // listview to show the data for this set the adapter to the listview
                // for the datasource i will use the array of the name
                // this is the data source
                String[] names = {"Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay","Ajay"};
        
                // this is the adapter
                ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, names);
                // Listview initialized
                ListView lv = (ListView) findViewById(R.id.lv);
                // adapter is set to listview
                lv.setAdapter(adapter);
        
                // the end
            }
        
        }

> If you are confused , just copy the above code and paste it, they are 101% working and tested code.

**Output**

![Output](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-2/ch2_1.png)

*Simpler huhh!!! lets move to little bit harder, dont worry its not that much hard though. Next lets see how to make custom layout.*

**Lets Make simple ContactList **
> We need the datasource so lets make a model for contacts.


    Contact.java
    package com.yuvi.myapplication;
    
    /**
     * Created by yubraj on 6/12/16.
     */
    public class Contact {
        public String name, contactnumber;
    
        public Contact(String name, String contactnumber){
            this.name = name;
            this.contactnumber = contactnumber;
        }
    }

>Then after create the layout for the row for contacts.

    row_contact.xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_vertical"
        android:minHeight="48dp"
        android:orientation="horizontal"
        android:padding="8dp">
    
        <!--Weight attribute is relative size with screen size
        this will automatically occupies the space leaving the image width-->
    
        <TextView
            android:id="@+id/tv_name"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Yubaraj Poudel"
            android:textColor="#212121"
            android:textSize="16sp" />
    
        <ImageView
            android:id="@+id/iv_call"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_call_black_24dp" />
    
    </LinearLayout>

**Output**

![Output](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-2/row_contact.png)

>Now all set, Lets Come to our main goal creating custom Adapter, create the new Activity CustomListViewActivity

    CustomListViewActivity.java
    
    package com.yuvi.myapplication;
    
    import android.Manifest;
    import android.content.Intent;
    import android.content.pm.PackageManager;
    import android.net.Uri;
    import android.os.Bundle;
    import android.support.annotation.Nullable;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.ArrayAdapter;
    import android.widget.ImageView;
    import android.widget.ListView;
    import android.widget.TextView;
    
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * Created by yubraj on 6/12/16.
     */
    public class CustomListViewActivity extends AppCompatActivity {
        @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            // set the layout to the activity, lets use the same activity
            setContentView(R.layout.list_activity);
            // Initialize the listview
    
            ListView lv = (ListView) findViewById(R.id.lv);
    
            // next lets create the datasource
            // we have the model Contact class
            // let create the list of the contact class
    
            // contactlist List is created of model Contact
            final List<Contact> contactList = new ArrayList<>();
            // populate the list with the data
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
            contactList.add(new Contact("YubarajPoudel", "9842583634"));
    
            // we are almost ready
            // Lets begin to make the custom adapter as we have name and phonenumber
            // array adapter is created with the adapter name
            ArrayAdapter<Contact> adapter = new ArrayAdapter<Contact>(this, R.layout.row_contact, contactList) {
                // this callback will give the view to adapter
                @Override
                public View getView(int position, View convertView, ViewGroup parent) {
                    // check whether the row layout is already inflated or not
                    // if not inflate the layout, iflation means convert the xml to java object
                    if (convertView == null) {
                        // initalize the object of the layout inflater to inflate
                        LayoutInflater inflater = getLayoutInflater();
                        // inflate the layout
                        // now the row_contact xml class is converted to View class
                        convertView = inflater.inflate(R.layout.row_contact, null);
                    }
                    // initialize the view in row_contact
                    TextView tv_name = (TextView) convertView.findViewById(R.id.tv_name);
                    ImageView iv_call = (ImageView) convertView.findViewById(R.id.iv_call);
                    // get the contact from the position
                    // this size of the list is always equals to datasource size ie here contact list size
    
                    // Single contact is extracted from the list by the position
                    Contact contact = contactList.get(position);
                    // set the data on the view
                    tv_name.setText(contact.name + "\n" + contact.contactnumber);
                    // now let make do the phone call when call image is clicked
    
                    iv_call.setOnClickListener(new View.OnClickListener() {
                        @Override
                        public void onClick(View view) {
                            Intent callIntent = new Intent(Intent.ACTION_CALL);
                            callIntent.setData(Uri.parse("tel:0377778888"));
                            if (ActivityCompat.checkSelfPermission(CustomListViewActivity.this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
                                // TODO: Consider calling
                                //    ActivityCompat#requestPermissions
                                // here to request the missing permissions, and then overriding
                                //   public void onRequestPermissionsResult(int requestCode, String[] permissions,
                                //                                          int[] grantResults)
                                // to handle the case where the user grants the permission. See the documentation
                                // for ActivityCompat#requestPermissions for more details.
                                return;
                            }
                                startActivity(callIntent);
    
                        }
                    });
    
    
    
                    return convertView;
                }
            };
    
        // set the adapter to the listview
            lv.setAdapter(adapter);
        }
    }

> Yes !!! Our finally all completed and if you have done everything correctly you will ended up seeing this layout.

![enter image description here](https://raw.githubusercontent.com/yuviii/LearnAndroid/master/ch-2/customlayoutscreen.png)

> Note: for Phone call action dont forget to put the call permission in the AndroidManifest.xml file

    <uses-permission android:name="android.permission.CALL_PHONE" />

> Its all done !!! Thanks for reading through this, I hope you have learned about the Listview and adapter. Keep on Looking more tutorials are comming.

Thanks!!!

