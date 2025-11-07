# MWAD

## AJAX
15. AJAX
Create a Calendar Navigation by using Ajax Calendar Extender control ,in which user can Navigate Year , Month and select date from the Calendar control.

Default.aspx
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="AjaxCalendarDemo._Default" %>
<%@ Register Assembly="AjaxControlToolkit" Namespace="AjaxControlToolkit" TagPrefix="asp" %>

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>AJAX Calendar Navigation</title>
    <style>
        body {
            font-family: Arial;
            margin: 50px;
            text-align: center;
        }
        .calendar {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <form id="form1" runat="server">
        <asp:ScriptManager ID="ScriptManager1" runat="server" />
        <h2>📅 AJAX Calendar Navigation</h2>
        <hr />
        <div>
            <asp:Label ID="Label1" runat="server" Text="Select a Date: "></asp:Label>
            <asp:TextBox ID="txtDate" runat="server" ReadOnly="true"></asp:TextBox>
            <asp:ImageButton ID="imgCalendar" runat="server" ImageUrl="~/calendar.png" AlternateText="Calendar" />
            
            <asp:CalendarExtender ID="CalendarExtender1" runat="server"
                TargetControlID="txtDate"
                PopupButtonID="imgCalendar"
                Format="dd-MM-yyyy">
            </asp:CalendarExtender>
        </div>

        <div class="calendar">
            <asp:Label ID="lblSelectedDate" runat="server" Text="No date selected"></asp:Label>
        </div>

        <br />
        <asp:Button ID="btnShowDate" runat="server" Text="Show Selected Date" OnClick="btnShowDate_Click" />
    </form>
</body>
</html>


Default.aspx.cs

using System;

namespace AjaxCalendarDemo
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnShowDate_Click(object sender, EventArgs e)
        {
            if (!string.IsNullOrEmpty(txtDate.Text))
            {
                lblSelectedDate.Text = "You selected: " + txtDate.Text;
            }
            else
            {
                lblSelectedDate.Text = "Please select a date first!";
            }
        }
    }
}

Web.config

<?xml version="1.0"?>
<configuration>
  <system.web>
    <compilation debug="true" targetFramework="4.8" />
    <httpRuntime targetFramework="4.8" />
  </system.web>
</configuration>

---------------------------------------------------------------------------------------

## LINQ and Stored Procedure
14. LINQ and Stored Procedure

Create database train_master having table train_info and have fields like train_id (PK, AI), train_name, train_type,
arrival_time, departure_time, start_location, end _location. Create web page traindata.aspx that allow user to enter train_id, train_name, train_type, arrival_time, departure_time, start_location, end_location and perform following operations Insert, delete, view, update, search [Perform this application using LINQ and Stored Procedure]


1) SQL: Database, Table & Stored Procedures
Run in SQL Server:
CREATE DATABASE train_master;
GO
USE train_master;
GO

CREATE TABLE train_info (
    train_id INT IDENTITY(1,1) PRIMARY KEY,
    train_name NVARCHAR(100),
    train_type NVARCHAR(50),
    arrival_time TIME,
    departure_time TIME,
    start_location NVARCHAR(100),
    end_location NVARCHAR(100)
);
GO

-- Insert
CREATE PROC sp_InsertTrain
 @train_name NVARCHAR(100), @train_type NVARCHAR(50),
 @arrival_time TIME, @departure_time TIME,
 @start_location NVARCHAR(100), @end_location NVARCHAR(100)
AS
INSERT INTO train_info VALUES(@train_name,@train_type,@arrival_time,@departure_time,@start_location,@end_location);
GO

-- Update
CREATE PROC sp_UpdateTrain
 @train_id INT, @train_name NVARCHAR(100), @train_type NVARCHAR(50),
 @arrival_time TIME, @departure_time TIME,
 @start_location NVARCHAR(100), @end_location NVARCHAR(100)
AS
UPDATE train_info SET train_name=@train_name, train_type=@train_type,
 arrival_time=@arrival_time, departure_time=@departure_time,
 start_location=@start_location, end_location=@end_location
WHERE train_id=@train_id;
GO

-- Delete
CREATE PROC sp_DeleteTrain @train_id INT
AS DELETE FROM train_info WHERE train_id=@train_id;
GO

-- View
CREATE PROC sp_GetTrains AS SELECT * FROM train_info;
GO

-- Search
CREATE PROC sp_SearchTrain @keyword NVARCHAR(100)
AS
SELECT * FROM train_info 
WHERE train_name LIKE '%'+@keyword+'%' OR train_type LIKE '%'+@keyword+'%';
GO

2) Add LINQ to SQL in Visual Studio
Add new LINQ to SQL Classes file → name it TrainData.dbml.
Drag train_info table and all stored procedures into the designer.
Save — it creates TrainDataDataContext.

3) traindata.aspx (UI)
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="traindata.aspx.cs" Inherits="traindata" %>
<!DOCTYPE html>
<html>
<body>
<form runat="server">
    Train ID: <asp:TextBox ID="txtID" runat="server" ReadOnly="true" /><br/>
    Name: <asp:TextBox ID="txtName" runat="server" /><br/>
    Type: <asp:TextBox ID="txtType" runat="server" /><br/>
    Arrival: <asp:TextBox ID="txtArrival" runat="server" /><br/>
    Departure: <asp:TextBox ID="txtDeparture" runat="server" /><br/>
    Start: <asp:TextBox ID="txtStart" runat="server" /><br/>
    End: <asp:TextBox ID="txtEnd" runat="server" /><br/>

    <asp:Button ID="btnInsert" runat="server" Text="Insert" OnClick="btnInsert_Click" />
    <asp:Button ID="btnUpdate" runat="server" Text="Update" OnClick="btnUpdate_Click" />
    <asp:Button ID="btnDelete" runat="server" Text="Delete" OnClick="btnDelete_Click" />
    <asp:TextBox ID="txtSearch" runat="server" />
    <asp:Button ID="btnSearch" runat="server" Text="Search" OnClick="btnSearch_Click" />
    <asp:Button ID="btnShow" runat="server" Text="Show All" OnClick="btnShow_Click" />

    <br/><asp:GridView ID="GridView1" runat="server" AutoGenerateColumns="true" />
</form>
</body>
</html>

4) traindata.aspx.cs (Code-behind)
using System;
using System.Linq;

public partial class traindata : System.Web.UI.Page
{
    TrainDataDataContext db = new TrainDataDataContext();

    protected void Page_Load(object sender, EventArgs e)
    {
        if (!IsPostBack) ShowAll();
    }

    void ShowAll()
    {
        GridView1.DataSource = db.sp_GetTrains();
        GridView1.DataBind();
    }

    protected void btnInsert_Click(object sender, EventArgs e)
    {
        db.sp_InsertTrain(txtName.Text, txtType.Text, TimeSpan.Parse(txtArrival.Text),
                          TimeSpan.Parse(txtDeparture.Text), txtStart.Text, txtEnd.Text);
        ShowAll();
    }

    protected void btnUpdate_Click(object sender, EventArgs e)
    {
        db.sp_UpdateTrain(int.Parse(txtID.Text), txtName.Text, txtType.Text,
                          TimeSpan.Parse(txtArrival.Text), TimeSpan.Parse(txtDeparture.Text),
                          txtStart.Text, txtEnd.Text);
        ShowAll();
    }

    protected void btnDelete_Click(object sender, EventArgs e)
    {
        db.sp_DeleteTrain(int.Parse(txtID.Text));
        ShowAll();
    }

    protected void btnSearch_Click(object sender, EventArgs e)
    {
        GridView1.DataSource = db.sp_SearchTrain(txtSearch.Text);
        GridView1.DataBind();
    }

    protected void btnShow_Click(object sender, EventArgs e)
    {
        ShowAll();
    }
}

--------------------------------------------------------------------------------------

## Dynamic generation of widgets

Practical – 03
Develop an application that accepts a number from user. The application should dynamically generate accepted number of list items in another activity.

activity_main.XML
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/inp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint='Enter a number:'
        android:inputType="numberDecimal"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.497"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.304" />

    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Submit"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/inp"
        app:layout_constraintVertical_bias="0.128" />
</androidx.constraintlayout.widget.ConstraintLayout>

Mainactivity.java

package com.example.tempgen;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    private EditText inp;
    private Button btn;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        //coding start from here

        inp=findViewById(R.id.inp);
        btn=findViewById(R.id.btn);
        btn.setOnClickListener(v->{
            String s=inp.getText().toString().trim();
            if(s.isEmpty()){
                Toast.makeText(this, "Input Field is Empty!", Toast.LENGTH_SHORT).show();
                return;
            }
            int count=Integer.parseInt(s);
            Intent intent=new Intent(MainActivity.this,SecondActivity.class);
            intent.putExtra("count",count);
            startActivity(intent);
        });
    }
}

activity_second.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".SecondActivity">

    <ListView
        android:id="@+id/lv"
        android:layout_width="310dp"
        android:layout_height="438dp"
        android:scrollbars="vertical"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.495"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.61" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="119dp"
        android:layout_height="50dp"
        android:text="List View:"
        android:textSize="24sp"
        app:layout_constraintBottom_toTopOf="@+id/lv"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.534"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.798" />
</androidx.constraintlayout.widget.ConstraintLayout>

SecondActivity.java

package com.example.tempgen;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class SecondActivity extends AppCompatActivity {
    private ListView lv;
    ArrayList<String> arrayList;
    ArrayAdapter<String> adapter;
    @SuppressLint("MissingInflatedId")
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_second);

        lv=findViewById(R.id.iv);
        int count=getIntent().getIntExtra("count",0);
        arrayList=new ArrayList<>();
        for (int i=1;i<=count;i++){
            arrayList.add("Item "+i);
        }
        adapter=new ArrayAdapter<>(this, android.R.layout.simple_list_item_1,arrayList);
        lv.setAdapter(adapter);
    }
}

## Develop an android application as a mini browser that performs the following tasks:
## 1. Accept the URL from user in EditText 
## 2. On clicking Go, display the content of web page inside web view
## 3. Buttons for back and forward

### MainActivity.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="8dp"
    tools:context=".MainActivity">

    <!-- URL Input and Go Button -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        android:layout_marginBottom="8dp">

        <EditText
            android:id="@+id/url_input_edit_text"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="Enter URL (e.g., google.com)"
            android:inputType="textUri"
            android:padding="12dp"
            android:textSize="16sp"
            android:imeOptions="actionGo"
            android:background="@drawable/rounded_edittext_bg"/>

        <Button
            android:id="@+id/go_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Go"
            android:minWidth="0dp"
            android:paddingStart="16dp"
            android:paddingEnd="16dp"
            android:layout_marginStart="8dp"/>

    </LinearLayout>

    <!-- Back and Forward Navigation Buttons -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        android:layout_marginBottom="8dp">

        <Button
            android:id="@+id/back_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Back"
            android:drawableLeft="@android:drawable/ic_media_previous"
            style="?attr/materialButtonOutlinedStyle"
            android:enabled="false"
            android:layout_marginEnd="16dp"/>

        <Button
            android:id="@+id/forward_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Forward"
            android:drawableRight="@android:drawable/ic_media_next"
            style="?attr/materialButtonOutlinedStyle"
            android:enabled="false"/>

    </LinearLayout>

    <!-- WebView for displaying web content -->
    <WebView
        android:id="@+id/web_view"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"/>

    <!-- Simple drawable for EditText background (for aesthetics) -->
    <!-- You would need to create this file in res/drawable/rounded_edittext_bg.xml -->
    <!-- Example content for rounded_edittext_bg.xml:
    <shape xmlns:android="http://schemas.android.com/apk/res/android">
        <solid android:color="@android:color/white"/>
        <corners android:radius="8dp"/>
        <stroke android:color="#CCCCCC" android:width="1dp"/>
    </shape>
    -->

</LinearLayout>



### MainActivity.java
package com.example.minibrowser;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.view.KeyEvent;
import android.view.View;
import android.view.inputmethod.EditorInfo;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    private EditText urlEditText;
    private Button goButton;
    private Button backButton;
    private Button forwardButton;
    private WebView webView;

    // Default URL to load when the app starts
    private static final String DEFAULT_URL = "https://www.google.com";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 1. Initialize UI components
        urlEditText = findViewById(R.id.url_input_edit_text);
        goButton = findViewById(R.id.go_button);
        backButton = findViewById(R.id.back_button);
        forwardButton = findViewById(R.id.forward_button);
        webView = findViewById(R.id.web_view);

        // 2. Configure the WebView
        configureWebView();

        // 3. Set up listeners
        setupListeners();

        // Load the initial page
        loadUrl(DEFAULT_URL);
    }

    private void configureWebView() {
        // Must set a custom WebViewClient to keep links opening within this WebView, not the external browser
        webView.setWebViewClient(new CustomWebViewClient());

        // Enable JavaScript for most modern websites
        WebSettings webSettings = webView.getSettings();
        webSettings.setJavaScriptEnabled(true);
        // Zooming features
        webSettings.setBuiltInZoomControls(true);
        webSettings.setDisplayZoomControls(false);

        // Hide scrollbars for a cleaner look
        webView.setScrollBarStyle(View.SCROLLBARS_INSIDE_OVERLAY);
    }

    private void setupListeners() {
        // Go Button Listener
        goButton.setOnClickListener(v -> handleGoButtonClick());

        // Back Button Listener
        backButton.setOnClickListener(v -> {
            if (webView.canGoBack()) {
                webView.goBack();
            }
        });

        // Forward Button Listener
        forwardButton.setOnClickListener(v -> {
            if (webView.canGoForward()) {
                webView.goForward();
            }
        });

        // Handle "Go" action on the keyboard (IME_ACTION_GO)
        urlEditText.setOnEditorActionListener((v, actionId, event) -> {
            if (actionId == EditorInfo.IME_ACTION_GO ||
                (event != null && event.getKeyCode() == KeyEvent.KEYCODE_ENTER && event.getAction() == KeyEvent.ACTION_DOWN)) {
                handleGoButtonClick();
                return true; // Consume the event
            }
            return false;
        });
    }

    /**
     * Handles the logic for the "Go" button click or keyboard "Go" action.
     */
    private void handleGoButtonClick() {
        String url = urlEditText.getText().toString().trim();
        if (!url.isEmpty()) {
            loadUrl(url);
        } else {
            Toast.makeText(this, "Please enter a URL.", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Ensures the URL has a protocol and loads it into the WebView.
     */
    private void loadUrl(String url) {
        if (!url.startsWith("http://") && !url.startsWith("https://")) {
            url = "https://" + url;
        }
        webView.loadUrl(url);
    }

    /**
     * Updates the enabled/disabled state of the Back/Forward buttons based on WebView history.
     */
    private void updateNavigationButtons() {
        backButton.setEnabled(webView.canGoBack());
        forwardButton.setEnabled(webView.canGoForward());
    }

    /**
     * Custom WebViewClient to manage navigation and history updates.
     */
    private class CustomWebViewClient extends WebViewClient {
        @Override
        public void onPageFinished(WebView view, String url) {
            super.onPageFinished(view, url);
            // 1. Update the URL bar with the currently loaded URL
            urlEditText.setText(url);
            // 2. Update the state of the back/forward buttons
            updateNavigationButtons();
        }
    }

    /**
     * Override the back button to navigate WebView history instead of closing the activity.
     */
    @Override
    public void onBackPressed() {
        if (webView.canGoBack()) {
            webView.goBack();
        } else {
            // Allow the default system behavior (closing the app/activity) if no history exists
            super.onBackPressed();
        }
    }
}

### AndroidManifest.xml

<uses-permission android:name="android.permission.INTERNET" />






