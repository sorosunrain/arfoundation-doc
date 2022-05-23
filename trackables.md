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

## Plane Detection

## Meshing [](#mesh)