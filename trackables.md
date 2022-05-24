## Trackable managers

在ARFoundation中，"trackable"可以是真实世界中检测到并最总的任何事物，地面、点云、锚点、环境探针、人脸、图片或者3d物体都是可以被追踪的trackables。

每个trackable都有一个trackable manager。所有的managers都必须在同一个ARSessionOrigin上，这是因为ARSessionOrigin定义了所有的可被检测到的trackables的相对的转换。trackable manager使用ARSessionOrigin当前的位置去生成检测到的trackables到场景中。

以下是ARSessionOrigin上所有的trackable managers:

![](media/ar-session-origin-with-managers.png)

以下表格概述trackable manager以及对应的trackables:

| Trackable Manager | Trackable | 含义(Purpose) | 
| -- | -- | -- |
| ARPlaneManager | ARPLane | 发现平坦的表面 |
| ARPointCloudManager | ARPointCloud | 发现特征点 |
| ARAnchorManager | ARAnchor | 管理锚点，你可以使用如下的api动态的添加和删除它们:<br>`ARAnchorManager.AddAnchor` <br>`ARAnchorManager.RemoveAnchor` |
| ARRaycastManager | ARRaycast | 自动重复和更新射线? |
| ARTrackedImageManager | ARTrackedImage | 发现并追踪2D图像 |
| AREnvironmentProbeManager | AREnvironmentProbe | 创建当前环境的cubemap |
| ARFaceManager | ARFace | 检测跟踪人脸 | 
| ARTrackedObjectManager | ARTrckedObject | 检测3D物体 | 
| ARParticipantManager | ARParticipant | 管理多用户的session |

每一个trackable组件都存储了关于这个trackable的信息，
但无法可视化，当AR设备report一个update的时候，它负责去update transform。

### Enabling and disabling features

开启和关闭某项功能，只需要disable对应的Manager即可。比如某项耗费性能的功能在不使用的时候可以关闭。

### Enumerating trackables

Tracables可以使用对应Manager的trackables变量来读取遍历，例如:
```C#
var planeManager = GetComponent<ARPlaneManager>();
foreach (ARPlane plane in planeMnager.trackables)
{
    // Do something with the ARPlane
}
```

tracables属性返回一个集合，你还可以通过TryGetTrackable方法来获取。

### Trackable lifetime

每个trackable都能够被added、updated、removed。在每一帧中，managers都会查询上一帧的改变，每个manager都有一个可被订阅的事件:

| Trackable Manager | Event | 
| -- | -- |
| ARPlaneManager | planesChanged |
| ARPointCloudManager | pointCloudsChanged |
| ARAnchorManager | anchorsChanged |
| ARTrackedImageManager	| trackedImagesChanged |
| AREnvironmentProbeManager	| environmentProbesChanged |
| ARFaceManager	| facesChanged |
| ARTrackedObjectManager | trackedObjectsChanged |
| ARParticipantManager | participantsChanged |

一个trackable在updated和remoed之前必须added。

### Adding and removing trackables

一些trackables，例如anchors和环境探针，可以被手动的添加和删除。其它的trackables，例如planes，是自动添加和删除。有些可以手动添加和创建，如果支持，相关的managers会提供添加和删除的方法。

你不能直接Destroy一个trackable组件或者它的GameObject，支持手动删除的trackables，它的providers会提供响应的删除方法。例如要删除anchor，你需要第阿勇ARAnchorManager的RemoveAnchor方法。

当你手动add了一个trackable，底层子系统可能不会立即跟踪它。在子系统报告它将要被添加之前你不会收到他的added事件(通常会是在下一帧)，在手动添加到收到added事件的这段时间，trackable处于”pending“状态，可以在任何trackable上使用pending属性来检测。具体pending状态耗费的时间，是由低层决定的。

当trackable收到移除的通知后，它的manager会Destroy这个tracable的GameObject，除非destroyOnDemoval是false。

![](media/ar-plane.png)

**Deactivating existing trackables**

有时候，你可能想在不关闭manager的情况下停止一个trackable。例如你希望在不停止地面检测的情况下停止地面的渲染，要想实现这一点，可以deactive每个trackable的GameObject:

```C#
var planeManager = GetComponent<ARPlaneManager>();
foreach(var plane in planeManager.trackables){
    plane.gameOBject.SetActive(false);
}
```

### Controlling a trackable's GameObject

当一个新的tackable被检测到的时候，它的manager会初始化一个Prefab。这个初始化的GameObject一定要有一个与之类型对应的ARTrackable组件。如果这个Prefab是null，系统会创建一个只包含先关ARTrackable组件的GameObject。如果你的Prefab没有相对的ARTrackable，系统会添加一个。

例如，当plane manager见到一个plane的时候，如果指定了”Plane Prefab“，manager会创建它的GameObject，否则创建一个空的GameObject，然后为其添加ARPlane组件。

## Plane Detection
## Image Tracking
## Object Tracking
## Face Tracking
## Anchors
## Raycasts
## Point Clouds
## Environment Probes
## Participants
## Meshing