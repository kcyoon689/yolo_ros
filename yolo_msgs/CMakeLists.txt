cmake_minimum_required(VERSION 3.8)
project(yolo_msgs)

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(std_msgs REQUIRED)
find_package(vision_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/BoundingBox3D.msg"
  "msg/Mask.msg"
  "msg/KeyPoint2D.msg"
  "msg/KeyPoint2DArray.msg"
  "msg/KeyPoint3D.msg"
  "msg/KeyPoint3DArray.msg"
  "msg/Detection.msg"
  "msg/DetectionArray.msg"
  "srv/SetClasses.srv"
  DEPENDENCIES std_msgs vision_msgs geometry_msgs
)

ament_package()
