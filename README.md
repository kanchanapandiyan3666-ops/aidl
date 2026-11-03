# Ex.No:2 Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.The client app calls the service and changes the button's colour within the Main activity.


## AIM:

To Develop an android application to implement the AIDL server and client app. The server app hosts a Bound Service and contains the logic to return random colours to its client.
The client app calls the service and changes the button's colour within the Main activity using AIDL interface in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Griaffe )

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as CSAIDL and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display message give in MainActivity file(client/server).

Step 7: Save and run the application.

## PROGRAM:
```
Program to print the client/server services using AIDL”.
Developed by:KANCHANA P
Registeration Number :212225230125
```
## SERVER:
# AndroidManifestfest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:label="Emergency Contact Service"
        android:supportsRtl="true"
        android:theme="@android:style/Theme.Material.Light.NoActionBar">

        <service
            android:name=".EmergencyContactService"
            android:exported="true">

            <intent-filter>
                <action android:name="com.example.aidlserver.IEmergencycontactservice" />
            </intent-filter>

        </service>

    </application>

</manifest>
```
## Emergencycontactservice.java
```

package com.example.aidlserver;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.util.Log;

public class EmergencyContactService extends Service {

    private final IEmergencycontactservice.Stub binder =
            new IEmergencycontactservice.Stub() {

                @Override
                public void basicTypes(
                        int anInt,
                        long aLong,
                        boolean aBoolean,
                        float aFloat,
                        double aDouble,
                        String aString) {

                    Log.d("AIDL_SERVER", "Integer: " + anInt);
                    Log.d("AIDL_SERVER", "Long: " + aLong);
                    Log.d("AIDL_SERVER", "Boolean: " + aBoolean);
                    Log.d("AIDL_SERVER", "Float: " + aFloat);
                    Log.d("AIDL_SERVER", "Double: " + aDouble);
                    Log.d("AIDL_SERVER", "Message: " + aString);
                }
            };

    @Override
    public IBinder onBind(Intent intent) {
        return binder;
    }
}
```
## IEmergencycontactservice.aidl
```
package com.example.aidlserver;
interface IEmergencycontactservice {

    void basicTypes(
            int anInt,
            long aLong,
            boolean aBoolean,
            float aFloat,
            double aDouble,
            String aString
    );
}

```
## CLIENT:
## AndroidManifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <queries>
        <package android:name="com.example.aidlserver" />
    </queries>
    <application
        android:allowBackup="true"
        android:label="AIDL Client"
        android:theme="@style/Theme.Aidlclient"
        android:supportsRtl="true">

        <activity
            android:name=".MainActivity"
            android:exported="true">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

        <service
            android:name=".Emergencycontactservice"
            android:exported="true" />

    </application>

</manifest>
```
## MainActivity.java
```
package com.example.aidlclient;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.os.RemoteException;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

import com.example.aidlserver.IEmergencycontactservice;

public class MainActivity extends AppCompatActivity {

    private EditText etInt;
    private EditText etLong;
    private EditText etBoolean;
    private EditText etFloat;
    private EditText etDouble;
    private EditText etString;
    private Button sendButton;
    private TextView status;

    private IEmergencycontactservice emergencyService;
    private boolean isBound = false;

    private final ServiceConnection serviceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            emergencyService = IEmergencycontactservice.Stub.asInterface(service);
            isBound = true;
            status.setText("Connected to Server");
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            emergencyService = null;
            isBound = false;
            status.setText("Disconnected from Server");
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etInt = findViewById(R.id.etInt);
        etLong = findViewById(R.id.etLong);
        etBoolean = findViewById(R.id.etBoolean);
        etFloat = findViewById(R.id.etFloat);
        etDouble = findViewById(R.id.etDouble);
        etString = findViewById(R.id.etString);
        sendButton = findViewById(R.id.sendButton);
        status = findViewById(R.id.status);

        bindEmergencyService();

        sendButton.setOnClickListener(v -> {
            try {
                int a = Integer.parseInt(etInt.getText().toString());
                long b = Long.parseLong(etLong.getText().toString());
                boolean c = Boolean.parseBoolean(etBoolean.getText().toString());
                float d = Float.parseFloat(etFloat.getText().toString());
                double e = Double.parseDouble(etDouble.getText().toString());
                String f = etString.getText().toString();

                if (!isBound || emergencyService == null) {
                    status.setText("Connect to Server First");
                    return;
                }

                emergencyService.basicTypes(a, b, c, d, e, f);
                status.setText("Data Sent Successfully");

            } catch (RemoteException ex) {
                status.setText("Remote Exception occurred");
            } catch (Exception ex) {
                status.setText("Please Enter Valid Data");
            }
        });
    }

    private void bindEmergencyService() {
        Intent intent = new Intent("com.example.aidlserver.IEmergencycontactservice");
        intent.setPackage("com.example.aidlserver");
        boolean bound = bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE);
        if (!bound) {
            Intent localIntent = new Intent(this, Emergencycontactservice.class);
            bindService(localIntent, serviceConnection, Context.BIND_AUTO_CREATE);
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (isBound) {
            unbindService(serviceConnection);
            isBound = false;
        }
    }
}
```
## IEmergencycontactservice.aidl
```
package com.example.aidlclient;
import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.os.RemoteException;
import android.util.Log;

import com.example.aidlserver.IEmergencycontactservice;

public class Emergencycontactservice extends Service {

    private static final String TAG = "EmergencyContactService";

    private final IEmergencycontactservice.Stub binder =
            new IEmergencycontactservice.Stub() {

                @Override
                public void basicTypes(int anInt, long aLong,
                                       boolean aBoolean, float aFloat,
                                       double aDouble, String aString)
                        throws RemoteException {

                    Log.d(TAG, "Integer: " + anInt);
                    Log.d(TAG, "Long: " + aLong);
                    Log.d(TAG, "Boolean: " + aBoolean);
                    Log.d(TAG, "Float: " + aFloat);
                    Log.d(TAG, "Double: " + aDouble);
                    Log.d(TAG, "String: " + aString);
                }
            };

    @Override
    public IBinder onBind(Intent intent) {
        return binder;
    }
}

```
## OUTPUT

## SERVER:

<img width="1535" height="813" alt="Screenshot 2026-11-03 091741" src="https://github.com/user-attachments/assets/9257e65f-5020-40c4-a701-2d8063caaf41" />
## CLIENT:

<img width="1535" height="811" alt="Screenshot 2026-11-03 091535" src="https://github.com/user-attachments/assets/a4b24906-d05d-4c83-8021-cf441f405be8" />
## RESULT
Thus a Simple Android Application to create a AIDL interface and communicate the process between client and server using AIDL interface in Android Studio is developed and executed successfully.
