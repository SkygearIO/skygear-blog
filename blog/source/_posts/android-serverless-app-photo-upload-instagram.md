---
title: Skygear tutorial - Building an Instagram-Like App with the Skygear Android SDK
date: 2017/03/29 11:39:00
icon: fa-code
categories: Tutorials
tags:
- tutorial
- android
- java
- instagram
---

Since most of the similar tutorials on the Internet target only iOS and Web developers, I wanted to write a tutorial about building an Instagram-like Android app using an open-source serverless backend, Skygear. With Skygear, you can save photos and other media to the Cloud Database, retrieve, and share it.

This app will have the following features:

- Sign up or sign in with username and password
- Upload photos to the cloud
- Retrieve all photos stored in the cloud to the app

[Skygear](https://skygear.io/) is an open-source BaaS that offers a ready-to-use server with a set a APIs for you to store and get data from the database. Using this BaaS, we can skip the hassle of setting up a server and designing the APIs. In this tutorial, I will focus on a core feature: the Cloud Database.

This is what you’ll learn in this tutorial:

- Setting up [Skygear](https://github.com/SkygearIO/skygear-SDK-Android).
- Retrieving and saving data in the cloud.
- Setting up custom adapters and item layout for RecyclerView.
- Coding an entire app with Java and Android Studio, from scratch.
- Working with RecyclerView and SwipeRefreshLayout.
- Using runnable for multithreading.

Set up Skygear
We need to sign up for Skygear and setup the [Skygear Android SDK](https://docs.skygear.io/guides/quickstart/android/) in the app first.

If you haven't done so, please follow the following steps to set up our app environment.

1. Get a Skygear account [here](https://portal.skygear.io/). An app project will be created for you automatically.
2. Follow this [Skygear Android Quickstart](https://docs.skygear.io/guides/quickstart/android/) and install the Skygear SDK in your app development environment (e.g. Android Studio).

Sign up with username and password
Next, let's implement the sign up screen.

In Skygear, you need to sign in as a user before you can save records to the database. There are two ways to do a sign up: either with a **login interface** or **anonymously** in the background.

In this tutorial we are going to create an interface for people to sign up or sign in with username and password.

Let's first work on the interface. Here, we want to have the following:

* 2 EditText elements:
* the username
* the password
* 2 buttons
* sign in
* sign up

Add the following codes in `activity_main.xml`:
<pre><code>&lt;EditText 
    android:layout_margin="10dp" 
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:hint="Username" 
    android:id="@+id/username" /&gt;

&lt;EditText 
    android:layout_margin="10dp" 
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:hint="Password" 
    android:id="@+id/password" 
    android:inputType="textPassword" /&gt;

&lt;LinearLayout 
    android:orientation="horizontal" 
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:id="@+id/button_holder" 
    android:gravity="center"&gt;

&lt;Button 
    android:layout_margin="10dp" 
    android:text="LOGIN" 
    android:layout_width="wrap_content" 
    android:layout_height="match_parent" 
    android:id="@+id/login" /&gt;

&lt;Button 
    android:layout_margin="10dp" 
    android:text="SIGNUP" 
    android:layout_width="wrap_content" 
    android:layout_height="match_parent" 
    android:id="@+id/signup" /&gt;

&lt;/LinearLayout&gt;
</code>
</pre>

Next, let's work on the sign in and signup method using the Skygear APIs.

Before adding the methods, we have to initialise Skygear in our current activity.

Then use the `onClick()` method to trigger the sign in and sign up method whenever a user clicks on the buttons.
We can do this inside the onCreate method of the activity:

&nbsp;
<pre><code>@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    //Initialise skygear in activity
    Container skygear = Container.defaultContainer(this);
    Database publicDB = skygear.getPublicDatabase();

    EditText usernameInput = (EditText) findViewById(R.id.username);
    EditText passwordInput = (EditText) findViewById(R.id.password);
    Button logIn = (Button) findViewById(R.id.login);
    Button signUp = (Button) findViewById(R.id.signup);

    logIn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            String username = usernameInput.getText().toString();
            String password = passwordInput.getText().toString();
            skygearSignIn(username, password);
        }
    });

    signUp.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            String username = usernameInput.getText().toString();
            String password = passwordInput.getText().toString();
            skygearSignUp(username, password);
        }
    });
}

public void skygearSignIn(final String username, final String password) {
    skygear.getAuth().loginWithUsername(username, password, new AuthResponseHandler() {
        @Override
        public void onAuthSuccess(Record user) {
            //Get user records
        }

        @Override
        public void onAuthFail(Error error) {
            //Show error
        }
    });
}

public void skygearSignUp(final String username, final String password) {
    skygear.getAuth().signUpWithUsername(username, password, new AuthResponseHandler() {
        @Override
        public void onAuthSuccess(Record user) {
             //Save user credentials
            //Get user records
        }

        @Override
        public void onAuthFail(Error error) {
            //Show error
        }
    });
}
</code>
</pre>

Important: Once again, you need to sign in as a user before saving data to the Skygear database.

Select a photo from the Gallery
After all the above works, it's finally time to upload photos.

In Skygear, images, videos, and other files are saved in the database as `Assets`.

First, create a button and set an `onClick()` method to trigger `pickImageFromGallery()`.

I chose to put the button in the app's action bar, you can do so by adding a new `.xml` file into the res &gt; menu folder.

If your project does not have that folder, create one.

Open a new `main_menu.xml` file with the following codes:

&nbsp;
<pre><code>&lt;!-- Menu layout for main activity --&gt;
&lt;?xml version="1.0" encoding="utf-8"?&gt;

&lt;menu xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:app="http://schemas.android.com/apk/res-auto"&gt;

&lt;item 
    android:id="@+id/upload_photo" 
    android:icon="@drawable/ic_action_upload_image" 
    android:title="Upload photo" 
    app:showAsAction="always"/&gt;

&lt;/menu&gt;</code></pre>
Remember to inflate the menu by putting the following codes in your `MainActivity.java` (but outside the `onCreate` method):
<pre><code>// inflate our menu in the main activity
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.main_menu, menu);
    return true;
}</code></pre>
And override the `onOptionsItemSelected` method for performing actions when the button is clicked:
<pre><code>// onOptionsItemSelected in main activity
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.upload_photo:
            pickImageFromGallery()
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}</code></pre>
&nbsp;

When a user clicks on the button, he will be directed to the Gallery of his phone to select the photo he wants to upload.

This is the code for `pickImageFromGallery()`method:
<pre><code>// method called when the pick image button is clicked
public static final int PICK_IMAGE = 1;<br>
void pickImageFromGallery(){
    Intent getIntent = new Intent(Intent.ACTION_GET_CONTENT);
    getIntent.setType("image/*");<br>
    Intent pickIntent = new Intent(Intent.ACTION_PICK, android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
    pickIntent.setType("image/*");<br>
    Intent chooserIntent = Intent.createChooser(getIntent, "Select Image");
    chooserIntent.putExtra(Intent.EXTRA_INITIAL_INTENTS, new Intent[] {pickIntent});<br>
    startActivityForResult(chooserIntent, PICK_IMAGE);
}</code></pre>

Then, `Override` the `onActivityResult()` to store the photo in a `byte[]`.

If you are not familiar with `byte`, you can check out this [article](http://www.computerhope.com/jargon/b/byte.htm).

**Android Tip**: You can convert bitmaps(image) to byte[] using **InputStream**.

&nbsp;
<pre><code>// the method triggered when user picked a photo
public byte[] currentImageData;
public String currentImageName, currentImageType;<br>
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data)
{
    if (requestCode == PICK_IMAGE) {
        if (data == null || resultCode != RESULT_OK) {
            return;
        }
        try {
            ContentResolver cR = getContentResolver();
            InputStream iStream = cR.openInputStream(imageUri);
            currentImageName = IMAGE_NAME;
            currentImageType = cR.getType(imageUri);
            currentImageData = Util.getBytes(iStream);

            //TO UPLOAD IMAGE THREAD
        } catch(Exception e){e.printStackTrace();}
    }
}</code></pre>
&nbsp;

After getting the desired image data in the form of `byte[]`, we can then upload it to the cloud.

Upload the selected photo to Skygear
In Skygear, a new `asset` is created with the following parameters:
1. Name of the `asset`
2. Mimetype of the `asset`
3. The image data in the form of `byte[]`

Remember we setup three variables (`currentImageName`, `currentImageType`, `currentImageData`) in the previous code? We can use those variables to upload the image.

Now, lets create an asset for our photo and upload it as an asset to Skygear:
<pre><code>Asset imageAsset = new Asset(imageName, imageType, imageData); 
publicDB.uploadAsset(imageAsset, new AssetPostRequest.ResponseHandler() { 
    @Override 
    public void onPostSuccess(Asset asset, String response) { 
        Log.i("Skygear Asset", "Successfully uploaded to " + asset.getUrl()); 
    }
    @Override 
    public void onPostFail(Asset asset, Error error) { 
        progressDialog.dismiss(); 
        Log.i("Skygear Asset", "Upload fail: " + error.toString()); 
        Toast.makeText(MainActivity.this, "Upload failed!", 
        Toast.LENGTH_SHORT).show(); 
    } 
});</code></pre>
Associate the image with a record
_Why?_ So that we can query the photo later and display it in our app.

This is how to associate the image asset we created earlier with a record in Skygear:
<pre><code>// creating a new Record and save it to skygear
Record aNote = new Record("Note");
aNote.set("image", asset);

publicDB.save(aNote, new RecordSaveResponseHandler(){
    @Override
    public void onSaveSuccess(Record[] records) {
        Log.i("Skygear Record", "Successfully saved");
    }

    @Override
    public void onPartiallySaveSuccess(Map&lt;String, Record&gt; successRecords, Map&lt;String, Error&gt; errors) {
        Log.i("Skygear Record", "Partially saved");
    }

    @Override
    public void onSaveFail(Error error) {
        Log.i("Skygear Record", "Record save fails");
    }
});</code></pre>
&nbsp;

Okay, so now our photo is stored in the database. How can we get it back?

Retrieve the photo and show it in a RecyclerView
Next, let's prepare a `RecyclerView` and the custom ArrayAdapter for the UI.

Put a simple RecyclerView in the activity:
<pre><code>&lt;!-- The RecyclerView for displaying uploaded photos --&gt;
&lt;android.support.v7.widget.RecyclerView 
    android:id="@+id/recycler_view" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent" /&gt;</code></pre>
Then open a new `list_item.xml` file in the `res &gt; layout` folder. This will be used as the layout of elements inside the `RecyclerView`.

&nbsp;
<pre><code>&lt;!-- The item layout for our RecyclerView adapter --&gt;
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
xmlns:app="http://schemas.android.com/apk/res-auto" android:orientation="vertical" android:layout_width="match_parent" 
android:layout_height="wrap_content"&gt;

&lt;ImageView 
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:adjustViewBounds="true" 
    android:id="@+id/photo" 
    android:scaleType="centerInside" /&gt;

&lt;/LinearLayout&gt;</code></pre>
&nbsp;

Next, create a new java class file named `PhotoAdapter.java`. This will be our custom adapter for the RecyclerView.

&nbsp;
<pre><code>// adapter for our RecyclerView
public class PhotoAdapter extends RecyclerView.Adapter&lt;PhotoAdapter.MyHolder&gt; {
    private ArrayList&lt;Photo&gt; photos;
    private Context mContext;

    public PhotoAdapter(Context context, ArrayList&lt;Photo&gt; photos) {
        this.mContext = context;
        this.photos = photos;
    }

    @Override
    public MyHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View inflatedView = LayoutInflater.from(parent.getContext())
            .inflate(R.layout.photo_item, parent, false);
        return new MyHolder(inflatedView);
    }

    @Override
    public void onBindViewHolder(final MyHolder holder, final int position) {
        final Photo curPhoto = photos.get(position);

        // Load information of photo into holder title
        holder.title.setText(format.format(curPhoto.getTime()));

        // Load photo with Picasso library
        Picasso.get()
            .load(curPhoto.getUrl())
            .into(holder.photo);
     }

    public long getItemId(int position) {
        return position;
    }

    @Override
    public int getItemCount() {
        return photos.size();
    }

    public void setPhotos(ArrayList&lt;Photo&gt; photos) {
        this.photos = photos;
        notifyDataSetChanged();
    }

    public static class MyHolder extends RecyclerView.ViewHolder {

        private TextView title;
        private ImageView photo;

        public MyHolder(View v) {
            super(v);
            title = (TextView) v.findViewById(R.id.title);
            photo = (ImageView) v.findViewById(R.id.photo);
        }
    }
}</code></pre>
&nbsp;

Here `Photo` is a custom class that contains a photo's url and created time data.

We can now perform a `query` to get the record we have just associated the photo with.

So lets contruct a query to get our records:
<pre><code>// new query object for fetching our photos
Query noteQuery = new Query("Note");<br>
publicDB.query(noteQuery, new RecordQueryResponseHandler() {
    @Override
    public void onQuerySuccess(Record[] records) {
        ArrayList&lt;Photo&gt; photoList = new ArrayList&lt;Photo&gt;();
        Log.i("Record Query", String.format("Successfully got %d records", records.length));
        for (Record record : photoRecords) {
            Asset photo = (Asset) record.get("image");
            photoList.add(new Photo(record, photo));
    }
    PhotoAdapter adapter = (PhotoAdapter) recyclerView.getAdapter();
    if(adapter == null) {
        final PhotoAdapter mAdapter = new PhotoAdapter(this, photoList);
        recyclerView.setAdapter(mAdapter);
    } else {
        adapter.setPhotos(photoList);
    }
}<br>
    @Override
    public void onQueryError(Error error) {
        Log.i("Record Query", String.format("Fail with reason:%s", error.getMessage()));
    }
});
</code></pre>
What the above code does:

1. Create an object of type `Photo` to associate the photo to our custom class
2. Put each of them into an `ArrayList` named photoList.
3. Create a new `PhotoAdapter` with the photoList and set the PhotoAdapter of our RecyclerView to the `PhotoAdapter`.

Hurray! You have learned the basics of saving and fetching photos (or any other file) with [Skygear](https://skygear.io/)!

**Android Tip**: You should run the above code in a new `Thread` to prevent heavy workload on the main thread.

Bonus: Pull to refresh
In social networking apps like Facebook and Instagram, users can refresh their newsfeed by pulling it down. This "pull to refresh" feature is quite simple to implement.

First, in your `activity_main.xml`, wrap your `RecyclerView` with a `SwipeRefreshLayout` like this:
<pre><code>&lt;!-- Swipe refresh layout --&gt;
&lt;android.support.v4.widget.SwipeRefreshLayout 
    android:id="@+id/swiperefresh" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent"&gt;<br>
&lt;android.support.v7.widget.RecyclerView 
    android:id="@+id/recycler_view" 
    android:layout_width="match_parent" 
    android:layout_height="match_parent" /&gt;<br>
&lt;/android.support.v4.widget.SwipeRefreshLayout&gt;
</code></pre>

Then, in you activity's `onCreate()` method, add the following lines:

<pre><code>// onRefreshListener in onCreate method of the main activity
((SwipeRefreshLayout) findViewById(R.id.swiperefresh)).setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
       //start fetching photo thread
    }
});</code></pre>

That is it.

[Skygear](https://skygear.io/) also offers other backend features, such as [chat](https://skygear.io/chat/) and [push notifications](https://docs.skygear.io/guides/push-notifications/basics/android/) that can be integrated into this app.

Additional

I have created a demo project for this tutorial. This is what you can do:

1. Get the project source code on [GitHub](https://github.com/Camerash/Skygear-photo-demo).
2. Play with the demo app online [here](https://appetize.io/app/3ywm9eqb0tzxw50rfgc7uf8khw).

What do you think about this tutorial? Share with me your thoughts!

If you would like support in using these features, get in touch with our team on the [Skygear Slack Channel](https://slack.skygear.io) or the [Skygear Forum](https://discuss.skygear.io/t/welcome-to-skygear-forum/8).
