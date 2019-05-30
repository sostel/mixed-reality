---
title: Recommended settings for Unity
description: Unity offers some behaviors specific to mixed reality that can be toggled through project settings.
author: Troy-Ferrell
ms.author: trferrel
ms.date: 03/26/2019
ms.topic: article
keywords: unity, settings, mixed reality
---

# Recommended settings for Unity

Unity provides a set of default options that are generally the average case for all platforms. However, Unity offers some behaviors specific to mixed reality that can be toggled through project settings.

## Performant environment set-up

### Low quality settings

It is important to modify the **Unity Quality settings** for your environment to **Very Low**. This will help ensure your application is running performantly at the appropriate framerate. This is extremely significant for Hololens development. For development on immersive headsets, depending on the specs of the desktop powering the VR experience, one can still achieve framerate without the lowest quality parameters. 

In Unity 2018 LTS+, the project's quality level can be set by:

Under **Edit** > **Project Settings** > **Quality** > Set the **Default** by clicking on the downward arrow to the **Very Low** quality level

### Lighting settings

Similar to Quality scene settings, it is important to set optimal Lighting settings for your Mixed Reality application. In Unity, the Lighting setting that will usually have the greatest  performance impact on your scene is **Realtime Global Illumination**. This can be turned off by going under **Window** > **Rendering** > **Lighting Settings** > **Realtime Global Illumination**. 

There is another lighting setting, **Baked Global Illumination**. This setting can provide performant and visually striking results on immersive headsets but is generally not applicable for HoloLens development. **Baked Global Illumniation** is only calculated for static GameObjects which are generally not found in HoloLens scenes due to the nature of an unknown and changing environment.

Please read [Global Illumination from Unity](https://docs.unity3d.com/Manual/GIIntro.html) for more information. 

>[!NOTE]
> **Realtime Global Illumination** is set **per-scene** and thus developers must save this property for every Unity scene in their project. 

### Single pass instancing rendering path

In Mixed Reality applications, the scene is rendered twice, once for each eye to the user. Compared to traditional 3D development, this effectively double the amount of work that needs to be computed. Thus, it is important to select the most efficient rendering path in Unity to save both on CPU and GPU time. Single pass instanced rendering optimizes the Unity rendering pipeline for Mixed Reality apps and thus it is recommended to enable this setting by default for every project. 

To enable this feature in your Unity Project
1)  Open **Player XR Settings** (go to **Edit** > **Project Settings** > **Player** > **XR Settings**)
2) Select **Single Pass Instanced** from the **Stereo Rendering Method** drop-down menu (**Virtual Reality Supported** checkbox must be checked)

Read the following articles from Unity for more details with this rendering approach.
- [How to maximize AR and VR performance with advanced stereo rendering](https://blogs.unity3d.com/2017/11/21/how-to-maximize-ar-and-vr-performance-with-advanced-stereo-rendering/)
- [Single Pass Instancing](https://docs.unity3d.com/Manual/SinglePassInstancing.html) 

>[!NOTE]
> One common issue with Single Pass Instanced Rendering occurs if developers already have existing custom shaders not written for instancing. After enabling this feature, developers may notice some GameObjects only render in one eye. This is because the associated custom shaders do not have the appropriate properties for instancing.
>
> See [Single Pass Stereo Rendering for HoloLens](https://docs.unity3d.com/Manual/SinglePassStereoRenderingHoloLens.html) from Unity for how to address this problem

### Enable depth buffer sharing

To achieve better hologram stability from the perception of the user, it is recommended to enable the **Depth Buffer Sharing** property in Unity. By turning this on, Unity will share the depth map produced by your application with the Windows Mixed Reality platform. The platform will then be able to better optimize hologram stability specifically for your scene for any given frame being rendered by your application.

To enable this feature in your Unity Project
1) Open **Player XR Settings** (go to **Edit** > **Project Settings** > **Player** > **XR Settings**)
2) Select the checkbox for **Enable Depth Buffer Sharing** under **Virtual Reality SDKs** > **Windows Mixed Reality** expansion (**Virtual Reality Supported** checkbox must be checked)

Further, it is recommended to select **16-bit depth** under the **Depth Format** setting in this panel, especially for Hololens Development. Selecting 16-bit compared to 24-bit will significantly reduce the bandwidth requirements as less data will need to be moved/processed.

>[!NOTE]
> Developers should beware of Z-fighting when changing these values along with the camera's near/far plane settings. Z-Fighting occurs when two gameobjects try to render to the same pixel and due to limitations in fidelity of the depth buffer (i.e z depth), Unity cannot discern which object is in front of the other. Developers will note a flickering between two game objects as they *fight* for the same z-depth value. This can be solved by switching to 24-bit depth format as there will be a larger range of values for each object to calculate upon for their z-depth from the camera.
>
> However, it is recommended, particularly for Hololens development, to modify the camera's near and far planes to a smaller range instead and retaining the 16-bit depth format. The z-depth is non-linearly mapped to the range of values along the near and far camera planes. This can be modified by selecting the *Main Camera* in your scene and under **Inspector**, change the **Near & Far Clipping Plane** values to reduce their range (i.e from 1000m to 100m or other x value, etc.)

### Building for IL2CPP

Unity has deprecated support for the .NET scripting backend and thus recommend developers to utilize **IL2CPP** for their UWP visual studio builds. Although this brings various advantages, building your visual studio solution from Unity for **Il2CPP** can be signficantly slower than the old .NET method. Thus, it is highly recommended to follow best practices for building **IL2CPP** to save on development iteration time.

1) Leverage incremental building by building your project to the same directory every time, re-using the pre-built files there
2) Disable anti-malware software scans for your project & build folders
   - Open **Virus & threat protection** under your Windows 10 settings app
   - Select **Manage Settings** under **Virus & threat protection settings**
   - Select **Add or remove exclusions** under the **Exclusions** section
   - Click **Add an exclusion** and select the folder contain your Unity project code and build outputs
3) Utilize an SSD for building

Please read [Optimizing Build Times for IL2CPP](https://docs.unity3d.com/Manual/IL2CPP-OptimizingBuildTimes.html) for more info.

> [!NOTE]
> Furthermore, it may be beneficial to setup a [Cache Server](https://docs.unity3d.com/Manual/CacheServer.html), especially for Unity projects with a large amount of assets (excluding script files) or constantly changing scenes/assets. When opening a project, Unity stores qualifying assets into an internal cache format on the developer machine. Items must be re-imported and thus re-processed when modified. This process can be done once and saved in a Cache Server and consequently shared with other developers to save time, instead of every developer processing the re-import of new changes locally.

## Publishing properties

### Holographic splash screen

HoloLens has a mobile-class CPU and GPU, which means apps may take a bit longer to load. While the app is loading, users will just see black, and so they may wonder what's going on. To reassure them during loading, you can add a holographic splash screen.

To toggle the holographic splash screen:
1) Go to **Edit** > **Project Settings** > **Player** page
2) Click on the **Windows Store** tab and open the **Splash Image** section
3) Apply your desired image under the **Windows Holographic > Holographic Splash Image** property.
    - Toggling the **Show Unity Splash Screen** option will enable or disable the Unity branded splash screen. If you do not have a Unity Pro license, the Unity branded splash screen will always be displayed.
    - If a **Holographic Splash Image** is applied, it will always be displayed regardless of whether the Show Unity Splash Screen checkbox is enabled or disabled. Specifying a custom holographic splash image is only available to developers with a Unity Pro license.

|  Show Unity Splash Screen  |  Holographic Splash Image  |  Behavior |
|----------|----------|----------|
|  On  |  None  |  Show default Unity splash screen for 5 seconds or until the app is loaded, whichever is longer. | 
|  On  |  Custom  |  Show Custom splash screen for 5 seconds or until the app is loaded, whichever is longer. | 
|  Off  |  None  |  Show transparent black (nothing) until app is loaded. | 
|  Off  |  Custom  |  Show Custom splash screen for 5 seconds or until the app is loaded, whichever is longer. | 

Please read [Unity's Splash Screen documentation](https://docs.unity3d.com/Manual/class-PlayerSettingsSplashScreen.html) for more info.

### Tracking loss

A Mixed reality headset depends on seeing the environment around it to construct [world-locked coordinate systems](coordinate-systems-in-unity.md), which allow holograms to remain in position. When the headset is unable to locate itself in the world, the headset is said to have *lost tracking*. In these cases, functionality dependent on world-locked coordinate systems, such as spatial stages, spatial anchors and spatial mapping, do not work.

If a loss of tracking occurs, Unity's default behavior is to stop rendering holograms, pause the [game loop](http://docs.unity3d.com/Manual/ExecutionOrder.html), and display a tracking lost notification that comfortably follows the users gaze. Custom notifications can also be provided in the form of a tracking loss image. For apps that depend upon tracking for their whole experience, it's sufficient to let Unity handle this entirely until tracking is regained. Developers can supply a custom image to be shown during tracking loss. 

To customize the tracking lost image:
1) Go to **Edit** > **Project Settings** > **Player** page
2) Click on the **Windows Store** tab and open the **Splash Image** section
3) Apply your desired image under the **Windows Holographic > Tracking Loss Image** property.

#### Opt-out of automatic pause

Some apps may not require tracking (e.g. [orientation-only apps](coordinate-systems-in-unity.md) such as 360-degree video viewers) or may need to continue processing uninterrupted while tracking is lost. In these cases, apps can opt out of the default loss of tracking behavior. Developers who choose this are responsible for hiding/disabling any objects which would not render properly in a tracking-loss scenario. In most cases, the only content that is recommended to be render in that case is body-locked content, centered around the main camera.

To opt out of automatic pause behavior:
1) Go to **Edit** > **Project Settings** > **Player** page
2) Click on the **Windows Store** tab and open the **Splash Image** section
3) Modify the **Windows Holographic > On Tracking Loss Pause and Show Image** checkbox.

#### Tracking loss events

To define custom behavior when tracking is lost, handle the global [tracking loss events](tracking-loss-in-unity.md).

### Capabilities

For an app to take advantage of certain functionality, it must declare the appropriate capabilities in its manifest. The manifest declarations can be made in Unity so they are included in every subsequent project export. 

Capabilities can be enabled for a Mixed Reality application by:
1) Go to **Edit** > **Project Settings** > **Player** page
2) Click on the **Windows Store** tab and open the **Publishing Settings** section and look for the **Capabilities** list

The applicable capabilities for enabling the commonly used APIs for Holographic apps are:
<br>

|  Capability  |  APIs requiring capability |
|----------|----------|
|  SpatialPerception  |  SurfaceObserver | 
|  WebCam  |  PhotoCapture and VideoCapture | 
|  PicturesLibrary / VideosLibrary  |  PhotoCapture or VideoCapture, respectively (when storing the captured content) | 
|  Microphone  |  VideoCapture (when capturing audio), DictationRecognizer, GrammarRecognizer, and KeywordRecognizer | 
|  InternetClient  |  DictationRecognizer (and to use the Unity Profiler) | 

## See also
* [Unity development overview](unity-development-overview.md)
* [Understaing performance for Mixed Reality](understanding-performance-for-mixed-reality.md)
* [Performance recommendations for Unity](performance-recommendations-for-unity.md)
