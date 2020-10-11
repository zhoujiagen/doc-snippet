# A Simple JVMTI Demo

## 菜谱

### 环境

- MacOS 10.13.3 (17D47)
- java version "1.8.0_172"
- Eclipse IDE for C/C++ Developers Version: 2019-09 R (4.13.0)
- CMake 3.17.0

### Eclipse CDT CMake项目

- 项目结构

```
cmake-demo-project
├── CMakeLists.txt
└── src
    ├── hello_world.cpp
    └── jvmti_agent.c
```

- CMakeLists.txt

``` CMake
cmake_minimum_required (VERSION 2.6)

project (cmake-demo-project)

include_directories(
	/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/include
	/Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/include/darwin
	src
)

add_executable(cmake-demo-project src/hello_world.cpp)
add_library(jvmti-agent SHARED src/jvmti_agent.c)

```

- jvmti_agent.c

``` C
#include <stdio.h>
#include <string.h>
// find / -type f -name "jvmti.h"
// /Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/include
#include "jni.h"
#include "jvmti.h"

static void
check_jvmti_errors (jvmtiEnv *jvmti, jvmtiError errnum, const char *str)
{

  if (errnum != JVMTI_ERROR_NONE)
    {

      char *errnum_str;
      errnum_str = NULL;
      (void) (*jvmti)->GetErrorName (jvmti, errnum, &errnum_str);
      printf ("ERROR: JVMTI: [%d] %s - %s", errnum,
	      (errnum_str == NULL ? "Unknown" : errnum_str),
	      (str == NULL ? "" : str));
    }

}

static void JNICALL callbackMethodEntry (jvmtiEnv *jvmti, JNIEnv *jni, jthread thread, jmethodID method)
  {

    char *name_ptr;
    char *signature_ptr;
    char *generic_ptr;
    jvmtiError error;
    error = (*jvmti)->GetMethodName (jvmti, method, &name_ptr, &signature_ptr,
	&generic_ptr);
    check_jvmti_errors (jvmti, error, "Unable to get the method name");
    printf ("Entered method %s\n", name_ptr);
  }

JNIEXPORT jint JNICALL
Agent_OnLoad (JavaVM *jvm, char *options, void *reserved)
{

  jvmtiEnv *jvmti;
  jvmtiError error;
  jint res;
  jvmtiEventCallbacks callbacks;
  jvmtiCapabilities capa;
  jrawMonitorID rawMonitorID;
  // Get the JVMTI environment

  res = (*jvm)->GetEnv (jvm, (void**) &jvmti, JVMTI_VERSION_1_0);
  if (res != JNI_OK || jvmti == NULL)
    {

      printf ("Unable to get access to JVMTI version 1.0");
    }

  (void) memset(&capa, 0, sizeof(jvmtiCapabilities));
  // Let's initialize the capabilities

  capa.can_generate_method_entry_events = 1;
  error = (*jvmti)->AddCapabilities (jvmti, &capa);
  check_jvmti_errors (jvmti, error, "Unable to add the required capabilities");
  // Setup event notification

  error = (*jvmti)->SetEventNotificationMode (jvmti, JVMTI_ENABLE,
					      JVMTI_EVENT_METHOD_ENTRY,
					      (jthread) NULL);
  check_jvmti_errors (jvmti, error,
		      "Unable to set the event notification mode");
  // Setup the callbacks

  (void) memset(&callbacks, 0, sizeof(callbacks));

  callbacks.MethodEntry = &callbackMethodEntry;
  error = (*jvmti)->SetEventCallbacks (jvmti, &callbacks,
				       (jint) sizeof(callbacks));
  check_jvmti_errors (jvmti, error, "Unable to set event callbacks");
  // Get the raw monitor

  error = (*jvmti)->CreateRawMonitor (jvmti, "JVMTI agent data", &rawMonitorID);
  check_jvmti_errors (jvmti, error, "Unable to create a Raw monitor");
  return JNI_OK;
}
```

- 生成静态库文件

```
libjvmti-agent.dylib
```

### Java示例程序

``` java
// HelloWorld.java
public class HelloWorld {
    public static void main(String[] args) {
    	System.out.println("Hello world!");
    }
}
```

### 运行

```
javac HelloWorld.java
java HelloWorld

LD_BIBRARY_PATH=`pwd` java -agentlib:jvmti-agent HelloWorld
```
