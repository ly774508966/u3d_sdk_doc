# Using SDK
Start to use SDK making VR apps!

## Integration stage

Only simple steps are needed to use SDK.

### 1.Importing SDK

After importing through Assets -> Import Package -> Custom Package, you will see the SDK structure:

	Assets\
		LingVR\
			Demo\				// demo scene
			Doc\				// where is the documents
			Materials\		    //
			Prefabs\				
			Resources\				
			Scripts\				
			Icon\					
		Plugins\				// Android Java and native libraries	

### 2.Project settings

#### Resolution and Presentation

* Set default oreitation to "Landscape Left"
* Uncheck `32-bit Display Buffer` if your project has performance issures

#### Other Settings

* Check `Multithreaded Rendering`
* Uncheck `Auto Graphics API`, and use `OpenGLES3` **only**
* Uncheck `Player setting -> Other Settings -> Virtual Reality Support` ( Avoid conflicts with other VR support )
* Set `Minimum API Level` to `Android 4.4 'Kit Kat'(API level 19)`
* Others are optional, see:

    ![Project settings](images/settings.png)

#### AndroidMenifest.xml

* Add following settings to AndroidMenifest.xml:

        <service android:name="com.lingvr.sensorbox.UdService3"></service>
    
        <uses-feature android:name="android.hardware.usb.host" />
    	<uses-permission android:name="com.android.example.USB_PERMISSION"/>
    	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

### 3.Replace your cameras with VR cameras

In LingVR SDK, VR cameras are under management of LvrManager, see the LvrManager prefab in `LingVR/Prefabs`

    LvrManager
        LeftEye
        Center
        RightEye

After dragging the `LvrManager` prefab into the scene and remove other cameras, you will get a game scene which support
LingVR, that's it. Then build the scene into apk to see if it works fine on android devices.

In editor, you can simulate head-rolling/look-around by holding Ctrl/ALT and moving cursor.

also LvrManager provides some parameters:

* Eye Texture Scale -> adjusting eye resolution, ranged from 0.0 ~ 1.0;
* Use Unity Remote Input -> whether to use [Unity Remote 4](http://docs.unity3d.com/Manual/UnityRemote4.html) to acquire phone sensor data.

### 4.Complete VR Game

There's still a important problems to be solved to make a complete VR game:

> How does the player interact with the game scene?

#### 1.Gaze Pointer

See the [demo project](http://www.lingvr.com/sdk/sdk.html) for more details

#### 2.Controller

We have used Android standard key value in BAI's controller:

|                |     |                      |
|----------------|-----|----------------------|
|      Back      | 4   | KEYCODE_BACK         |
|      Menu      | 103 | KEYCODE_BUTTON_R1    |
|    Volume+     | 24  | KEYCODE_VOLUME_UP    |
|    Volume-     | 25  | KEYCODE_VOLUME_DOWN  |
|       A        | 96  | KEYCODE_BUTTON_A     |
|       B        | 97  | KEYCODE_BUTTON_B     |
| Joystick up    | 19  | KEYCODE_DPAD_UP      |
| Joystick down  | 20  | KEYCODE_DPAD_DOWN    |
| Joystick left  | 21  | KEYCODE_DPAD_LEFT    |
| Joystick right | 22  | KEYCODE_DPAD_RIGHT   |


![joystick](images/joystick.png)

Attention：The Menu key here is actually mapping to the key R1. You need to do extra configratio in Edit -> Project Settings -> Input：

![joystick](images/r1button.png)

You could set `useController = true` under `LvrController` of LvrManager to activate controller, and then acquiring the current status of controller.

	kv = LvrController.keyValues;
	
`keyValues` is a `KeyValues` structure that contains every key's current status:

	public struct KeyValues
	{
		public bool buttonA;
		public bool buttonB;
		public bool buttonBack;
		public bool buttonR1;
		public float axisX;
		public float axisY;
	}
		
and then you can handle some events by acquiring some key values, for example:

	void Update()
	{
		if (kv.buttonA)
			attack();
		if (kv.buttonB)
			jump();
		if (kv.buttonBack)
			pause();
	}