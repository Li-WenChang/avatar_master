# Drake quick start
This README.md will give you a brief introduction about how to use drake to do robot simulation.

## 1. URDF?
This section covers how to make the URDF of **DexNex** compatible with **Drake**.  
If you're not working on a DexNex-related project, feel free to skip it!

### 1.1 What is URDF?
The **Unified Robot Description Format (URDF)** is an XML-based format used to describe a robot's physical and visual properties. At its core, a robot is a collection of **links** (rigid bodies) and **joints** (connections between links), and URDF specifies how these elements are connected.

URDF files define:
- The **type of joints** (e.g., fixed, revolute, prismatic)
- The **shape and appearance** of links (geometry, materials, colors)
- The **structure** of the robot (parent-child relationships)

For **visualization tools** like RViz, a URDF that includes shapes and colors is usually sufficient.

However, for **physics simulation** (e.g., in Drake, MuJoCo or Isaac Sim), you'll also need:
- **Mass**
- **Moments of Inertia (MOI)**
- **Collision geometry**
- **Transmission and actuator info**

These additional properties ensure that the robot behaves realistically in simulated environments.

### 1.2 Changes Needed for the Current URDF

After cloning `avatar_master` into `your_workspace/src` and building it, **do not generate the URDF yet**.  
Please stop and read this section first.

DexNex is composed of five robots: two arms, two hands, and one neck.
As mentioned earlier, simulators require more detailed information than visualizers.  

Currently, the URDF is missing:
- The **mass** for the neck (`xarm6`)
- Proper **actuator definitions** for the entire robot

We'll address these issues first to ensure the URDF is ready for simulation with Drake.  

#### 1.2.1 restore neck mass
The current urdf doesn't have the mass of neck (xarm6), we need to make the following change to get it back.  
Open this file `/home/user/your_workspace/src/avatar_master/avatar_driver/robots/avatar.urdf.xacro` and find this line: 
```xml
<xacro:include filename="$(find avatar_driver)/robots/xarm6_minimal.urdf.xacro" />
```
replace it with this line

```xml 
<xacro:include filename="$(find xarm_description)/urdf/xarm6/xarm6.urdf.xacro" />
```
#### 1.2.2 restore the transmission (actuators) of neck
Go to the folder `/home/user/your_workspace/src/xarm_ros2/xarm_description/urdf/xarm6` and you will see a file called `xarm6.transmission.xacro`. This means that the transmission has already been written by someone (the vendor I guess). Therefore, we only need to include this file  and call the macor in `xarm6.urdf.xacro`.  
Open `xarm6.urdf.xacro` and add the following two lines at line 266 (after the definition of joint 6):
```xml
<xacro:include filename="$(find xarm_description)/urdf/xarm6/xarm6.transmission.xacro"/>
<xacro:xarm6_transmission prefix="xarm6_" reduction="1"/>
```

#### 1.2.3 restore the transmission (actuators) of arm
Open the file `/home/li-wen/avatar_ws_backup_2/src/abb_gofa_ros2/abb_gofa_support/urdf/gofa_macro.xacro`
and add the following xml code at line 246 (after definition of joint 6 and before base link):
```xml
<!-- transmission list list -->
    
    <transmission name="${prefix}joint_1_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J1">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_1">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>

    <transmission name="${prefix}joint_2_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J2">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_2">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>

    <transmission name="${prefix}joint_3_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J3">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_3">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>

    <transmission name="${prefix}joint_4_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J4">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_4">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>

    <transmission name="${prefix}joint_5_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J5">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_5">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>
    
    <transmission name="${prefix}joint_6_transmission">
        <type>transmission_interface/SimpleTransmission</type>
        <actuator name="${prefix}J6">
            <mechanicalReduction>1</mechanicalReduction>
        </actuator>
        <joint name="${prefix}joint_6">
            <hardwareInterface>EffortJointInterface</hardwareInterface>
            <hardwareInterface>PositionJointInterface</hardwareInterface>
        </joint>
    </transmission>
    
    <!-- end of transmission list list -->
```
