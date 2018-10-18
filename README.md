# Africa's Talking

[ ![Download](https://api.bintray.com/packages/africastalking/android/com.africastalking/images/download.svg) ](https://bintray.com/africastalking/android/com.africastalking/_latestVersion)

This SDK simplifies the integration of Africa's Talking APIs into your Android apps. For better security,
the SDK is split into two components: A **server** module that stores API keys, SIP credentials and other secrets.
And a **client** module that runs in your app. This client module gets secrets from the server component (via RPC), and uses them to interact with the various APIs.

For instance, to send an SMS, the client will request a token from the server. The server will use it's API key to request a token from Africa's Talking on behalf of the client. It will then forward the token to the client which will use it to request the SMS API to send a text. All in a split second!


### Usage

Your server application could be something like this:

```java
/* On The Server (Java, Node.js, PHP, C/C++, C# and all languages supported by gRPC.) */

import com.africastalking.*;

public class SomeJavaApplication {

    public static void main(String[] args) {
    
        // Initialize the SDK
        AfricasTalking.initialize(USERNAME, API_KEY);
        
        // Initialize the server
        Server server = new Server();
        
        // Add SIP credentials (Voice Only)
        server.addSipCredentials(SIP_USERNAME, SIP_PASSWORD, SIP_HOST);
        
        // Start the server
        server.start();
        
        /*start
        UPDATE. Touch on the methods startInsecure(int port), start(File certChainFile, File privateKeyFile, int port), start(File certChainFile, File privateKeyFile) - port omitted, startInsecure() - port also ommitted and stop().
        
        All return void
        
        All these methods can start our server, I believe
        
        Why we need a read loop in this code? Can we fix it in the defined Server Class?
        
        */
        
        /*
        This code should be in a try{...} catch(){...} block. The Server class throws an IO exception
    }
}
```

And your Android app:

```java
/* On The Client (Android) */
public class SomeActivity extends Activity {
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(args);
        setContentView(R.layout.some_activity);
        
        // Init SDK
        AfricasTalking.initialize(SERVER_HOSTNAME);
        
        
        /*
        UPDATE
        
        initialize() is overloaded to 3 types:
        
        First, with (String host), second with (String host, int port), third with (String host, int port, boolean disableTLS)
        
        We then have initializeVoiceService(Activity context, RegistrationListener registrationListener, CallBack<VoiceService> callback
        
        All return void
        */
        
        /*
        The above statement should be in a try{...} catch{..} block. Throws an IOException. But looked at the method and no IO is being performed. So makes no sense to do this. Just feeding to the SDK static values of where your server details for resolution when actually accessing service.
        */
        
        /*
        Best advice for Android devs is to invoke the method above in onCreate() of their MainActivity (or whatever activity the android app launches from according to their manifest. 
        
        This is because this statement needs to be invoked once and not every other time a service is to be requested from Africa's Talking.
        */
       
       
       /*
       Modularize the doc to have the use of each service explained in its own section. First, what libraries of the SDK need to be imported. And finally, how would you use a service.
       */
       
       /*
       Update. This should be done in an AsyncTask. Actual network call here to the server. Standard android programming practice
       */
       
       /*
       Thus, this block of code should be in the doInBackground() method of the AsyncTask instance. 
       
       Also, should be in a try..catch block, because it throws an IOException (opening a port to communicate to the server)
       
       Show the code full in AsyncTask to make it easier for a beginner dev to easily integrate
       
       */
       
        // Get Service
        AirtimeService airtime = AfricasTalking.getAirtimeService();
        
        /*
        Now, the send() method is also overloaded. 2 with hashmaps (so for bulk smsing I suppose), 2 with String values.
        
        For each of the two distinct ones, one is separated from the other by a CallBack having the AirtimeResponse **not reponses**
        
        Those with CallBack return void, those without return an instance of AirtimeResponse.
        
        All statements involving the invocation of Africa's Talking services should be in a try catch block
        */
        
        
        // Use Service
        airtime.send("+25467675655", "KES", 100, new Callback<AirtimeResponses>() {
          @Override
          void onSuccess(AirtimeResponses responses) {
            //...
          }

          @Override
          void onError(Throwable throwable) {
            //...
          }
        });
    }
}
```

/*
Android SDK. Why do we have node? Might confuse dev that the node code is needed? Probably for the server only. Specify

Need to do a Kotlin of this? - simple code swap from Java to Kotlin?
*/

See the [example](./example) for complete sample apps (Android, Web Java+Node)

### Download

#### Server

**Node**

```shell
npm install --save africastalking
```

**Java**
/*
Specify on which gradle file this should be done - module's build.gradle..for your server
*/
```groovy
repositories {
  maven {
    url  "http://dl.bintray.com/africastalking/java"
  }
}
dependencies{
  compile 'com.africastalking:server:VERSION'
}
```

Or Maven (from `http://dl.bintray.com/africastalking/java`)

```xml
<dependency>
  <groupId>com.africastalking</groupId>
  <artifactId>server</artifactId>
  <version>VERSION</version>
</dependency>
```


#### Client (Android)

/*
Specify that it can be given as a project wide dependency or modular dependency....work with modular best.
```groovy

android {

    // ...
    
    defaultConfig {
        
        // ...
        
        ndk {
            abiFilters "armeabi", "x86"
        }
    }
}

repositories {
  maven {
    url  "http://dl.bintray.com/africastalking/android"
  }
}
dependencies{
  compile 'com.africastalking:client:VERSION'
  // or
  compile 'com.africastalking:client-ui:VERSION' // with checkout UI for payment
}
```



## Initialization
The following static methods are available on the `AfricasTalking` class to initialize the library:

- `initialize(String host, int port, bool disableTLS)`: Initialize the library.
- `getXXXService()`: Get an instance to a given `XXX` service. e.g. `AfricasTalking.getSmsService()`, `AfricasTalking.getPaymentService()`, etc.


## Services

All methods are synchronous (i.e. will block current thread) but provide asynchronous variants that take a `Callback<T>` as the last argument.

/*
Method that I'm yet to use... getUser()
*/
### `Account`
- `getUser()`: Get user information.

### `Airtime`

- `send(String phone, String currencyCode, float amount)`: Send airtime to a phone number.

- `send(HashMap<String, String> recipients)`: Send airtime to a bunch of phone numbers. The keys in the `recipients` map are phone numbers while the values are airtime amounts ( e.g. `KES 678`).

For more information about status notification, please read [http://docs.africastalking.com/airtime/callback](http://docs.africastalking.com/airtime/callback)

/*
Not used this method yet.
*/
### `Token`

- `createCheckoutToken(String phoneNumber)`: Create a checkout token.

/*
So many methods here. Not well documented

And I've noted what might be a redundancy between send() and sendBulk(). Similar params. Same logic?
*/

### `SMS`

- `send(String message, String[] recipients)`: Send a message

- `sendBulk(String message, String[] recipients)`: Send a message in bulk

- `sendPremium(String message, String keyword, String linkId, String[] recipients)`: Send a premium SMS

- `fetchMessage()`: Fetch your messages

- `fetchSubscription(String shortCode, String keyword)`: Fetch your premium subscription data

- `createSubscription(String shortCode, String keyword, String phoneNumber)`: Create a premium subscription

For more information on: 

- How to receive SMS: [http://docs.africastalking.com/sms/callback](http://docs.africastalking.com/sms/callback)

- How to get notified of delivery reports: [http://docs.africastalking.com/sms/deliveryreports](http://docs.africastalking.com/sms/deliveryreports)

- How to listen for subscription notifications: [http://docs.africastalking.com/subscriptions/callback](http://docs.africastalking.com/subscriptions/callback)

/*
Need to look at this too. Explain each method well. Links to more explanation of what the product is
*/
### `Payment`

- `checkout(CheckoutRequest request)`: Initiate checkout(mobile, card or bank).

- `validateCheckout(CheckoutValidateRequest request)`: Validate checkout (card or bank).

- `mobileB2C(String productName, List<Consumer> recipients)`: Send money to consumer. 

- `mobileB2B(String productName, Business recipient)`: Send money to business.

- `bankTransfer(String productName, List<Bank> recipients)`: Move money form payment wallet to bank account.

/*
Then best to do this in VoiceActivity
*/
### Voice

Unlike other services, voice is initialized as follows:

```java
AfricasTalking.initializeVoiceService(Context cxt, RegistrationListener listener, new Callback<VoiceService>() {
    @Override
    public void onSuccess(VoiceService service) {
      // keep a reference to the 'service'
    }

    @Override
    public void onFailure(Throwable throwable) {
      // something blew up
    }
});
```


- `registerCallListener(CallListener listener)`:

- `makeCall(String phoneNumber)`:

- `picCall()`:

- `holdCall()`:

- `resumeCall()`:

- `endCall()`:

- `sendDtmf(char character)`:

- `startAudio()`:

- `toggleMute()`:

- `setSpeakerMode(Context context, boolean loudSpeaker)`:

- `isCallInProgress()`:

- `getCallInfo()`

- `queueStatus(String phoneNumbers)`:

- `mediaUpload(String url)`:



## Requirements

On Android, This SDK requires **API 16+**. Your app will also need the following permissions:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.INTERNET" />
    
    <!-- The following are required if you want use the voice service -->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    
    <!-- ... -->
    
</manifest>
```

For more info, please visit [https://www.africastalking.com](https://www.africastalking.com)
