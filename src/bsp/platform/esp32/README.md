##### Copyright (c) 2003-2018, Xively All rights reserved.

Due to peculiarities in the IDF SDK's build system, some headers in FreeRTOS and
LwIP require the presence of an `sdkconfig.h` file. That file is generated by the
SDK's build system at application build time, and it includes plenty of
application-specific options.

In order to keep the libxively.a binary application-agnostic, we need to build
the ESP32 BSP as its own "component", and exclude it from the Xively Client's
build system.

There are 3 key points to this implementation:

- Using a custom subdirectory inside `src/bsp/platform/esp32` prevents our build system
from compiling the BSP into `libxively.a`
- The component.mk file will tell the SDK how to build the BSP
- Your application's Makefile needs to tell the SDK where to find this extra component.
Just add a path to the component folder using `EXTRA_COMPONENT_DIRS`. See the ESP32
example

Finally, the `xi_bsp_hton.h` BSP header required by cn-cbor NEEDS to be included by
libxively, and also depends on LwIP. In order to avoid including lwip's headers,
we provide forward declarations of the functions that we need. Those declarations
are specific to this hardware platform and its LwIP setup; see lwip/def.h, and avoid
re-using this BSP's `xi_bsp_hton.h` on future BSPs.