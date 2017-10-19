#Reading Point Cloud data from PCD files
In this tutorial we will learn how to read point cloud data from a PCD file.
##The code
First, create a file called, let’s say, pcd_read.cpp in your favorite editor, and place the following code inside it:
```c++
#include <iostream>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>

int
main (int argc, char** argv)
{
  pcl::PointCloud<pcl::PointXYZ>::Ptr cloud (new pcl::PointCloud<pcl::PointXYZ>);

  if (pcl::io::loadPCDFile<pcl::PointXYZ> ("test_pcd.pcd", *cloud) == -1) //* load the file
  {
    PCL_ERROR ("Couldn't read file test_pcd.pcd \n");
    return (-1);
  }
  std::cout << "Loaded "
            << cloud->width * cloud->height
            << " data points from test_pcd.pcd with the following fields: "
            << std::endl;
  for (size_t i = 0; i < cloud->points.size (); ++i)
    std::cout << "    " << cloud->points[i].x
              << " "    << cloud->points[i].y
              << " "    << cloud->points[i].z << std::endl;

  return (0);
}
```
##The explanation
Now, let’s break down the code piece by piece.<br>
```c++
  pcl::PointCloud<pcl::PointXYZ>::Ptr cloud (new pcl::PointCloud<pcl::PointXYZ>);
  ```
creates a PointCloud<PointXYZ> boost shared pointer and initializes it.<br>
```C++
  if (pcl::io::loadPCDFile<pcl::PointXYZ> ("test_pcd.pcd", *cloud) == -1) //* load the file
  {
    PCL_ERROR ("Couldn't read file test_pcd.pcd \n");
    return (-1);
  }
  ```<br>
loads the PointCloud data from disk (we assume that test_pcd.pcd has already been created from the previous tutorial) into the binary blob.
Alternatively, you can read a PCLPointCloud2 blob (available only in PCL 1.x). Due to the dynamic nature of point clouds, we prefer to read them as binary blobs, and then convert to the actual representation that we want to use.
```C++
pcl::PCLPointCloud2 cloud_blob;
pcl::io::loadPCDFile ("test_pcd.pcd", cloud_blob);
pcl::fromPCLPointCloud2 (cloud_blob, *cloud); //* convert from pcl/PCLPointCloud2 to pcl::PointCloud<T>
```
reads and converts the binary blob into the templated PointCloud format, here using pcl::PointXYZ as the underlying point type.
Finally:
```C++
  for (size_t i = 0; i < cloud->points.size (); ++i)
    std::cout << "    " << cloud->points[i].x
              << " "    << cloud->points[i].y
              << " "    << cloud->points[i].z << std::endl;
```
is used to show the data that was loaded from file.
##Compiling and running the program

Add the following lines to your CMakeLists.txt file:
```C++
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(pcd_read)

find_package(PCL 1.2 REQUIRED)

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

add_executable (pcd_read pcd_read.cpp)
target_link_libraries (pcd_read ${PCL_LIBRARIES})
```
After you have made the executable, you can run it. Simply do:
```C++
$ ./pcd_read
```
You will see something similar to:
```C++
Loaded 5 data points from test_pcd.pcd with the following fields: x y z
  0.35222 -0.15188 -0.1064
  -0.39741 -0.47311 0.2926
  -0.7319 0.6671 0.4413
  -0.73477 0.85458 -0.036173
  -0.4607 -0.27747 -0.91676
  ```
Note that if the file test_pcd.pcd does not exist (either it hasn’t been created or it has been erased), you should get an error message such as:
```C++Couldn't read file test_pcd.pcd
```