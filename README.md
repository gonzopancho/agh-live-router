[AGH Live Router](http://piotrjurkiewicz.github.com/agh-live-router/)
=================

AGH Live Router is a Debian derivative distribtion for experimental routing. It consists of Click Modular Router, running in kernel mode, and XORP routing suite. System is already configured and ready-to-use, distributed as an .iso image.

Find out more in [presentation](http://prezi.com/iez6ctgkd8dt/agh-live-router/).


Details
-------

ISO file contains an image of a live system. Distribution is based on Debian 6.0 (Squeeze). It contains installed Click environment (linuxmodule version) and XORP suite. XORP starts automatically on system startup. XORP, during its initialization, loads Click modules. Therefore, router becomes operational automatically after system startup. Additionally, along with start of graphical environment, Clicky application starts. This allows fast verification of correctness of Click startup and configuration load.


Usage
-----

Bootable live USB drive can be created with the [UNetbootin](http://unetbootin.sourceforge.net/) application. After creation of USB drive, you may want to put router configuration files on the USB. The configuration will be automatically loaded on system startup. Configuration files should be placed in "config" directory on the USB drive. This directory should contain the following files:

    X:\config\config.boot
    X:\config\click_generator
  
The config.boot file contains XORP configuration. A XORP router must be configured to perform the desired operations. At very minimum, a router’s interfaces and FEA must be configured. A very simply configuration can be found [there](https://github.com/piotrjurkiewicz/agh-live-router/tree/master/configurations/simple/config). More information about writing XORP configurations you can find in the relevant XORP [manual section](http://xorp.run.montefiore.ulg.ac.be/latex2wiki/user_manual/configuration_overview).

In case of AGH Live Router system, configuration file should contain the following FEA section:

    fea {
        unicast-forwarding4 {
          disable: false
        }
        click {
    		disable: false
    		duplicate-routes-to-kernel: true
    
    		kernel-click {
    			disable: false
    			install-on-startup:	true
    			kernel-click-modules: "/usr/local/lib/proclikefs.ko:/usr/local/lib/click.ko"
    			mount-directory: "/click"
    			kernel-click-config-generator-file: "/etc/xorp/click_generator"
    		}
    
    		user-click {
    			disable: true
    			command-file: "/usr/local/bin/click"
    			command-extra-arguments: "-R"
    			command-execute-on-startup: true
    			control-address: 127.0.0.1
    			control-socket-port: 13000
    			startup-config-file: "/dev/null"
    			user-click-config-generator-file: "/etc/xorp/click_generator"
    		}
        }
    }

The click_generator file contains an AWK script, which generates the kernel-level Click configuration from the XORP configuration. The script is called on-demand by the FEA whenever the network interface information changes. The default script you can find [there](https://github.com/piotrjurkiewicz/agh-live-router/blob/master/configurations/simple/config/click_generator). You may not want to modify the generator script, unless you are making significant changes in router's forwarding mechanisms.


Running in VM
-------------

You can also run system directly from the ISO file in a virtual machine. We recommend using Oracle's VirtualBox. In VirtualBox you can simple create internal networks between virtual machines and therefore experiment with various topologies. However, you must carefully choose network adapter types. Our test showed that the best performance (40MB/s across 3 VMs on i5-3570k, one core) can be achieved using Intel PRO/1000 MT Desktop or Intel PRO/1000 T Server. Do not choose virtio-net adapter because Click's kernel module doesn't work with the driver, what results in very bad performance in that case. Furthermore, our tests showed that the better performance can be achieved with enabled IO APIC (System -> Enable IO APIC).

If you want to put configuration and package files into an ISO image (so the configuration will be automatically loaded on VM start), you can use UltraISO application (for Windows). It has a feature of editing ISO images and adding additional files.


Packages
--------

Distribution supports simple loading of Click packages. Packages modules, compiled as kernel modules (.ko) should be placed in "packages" directory. After placing .ko file in "packages" you should do the following things:

a) In config.boot file you should add path to module in "kernel-click modules":

    kernel-click-modules: "/usr/local/lib/proclikefs.ko:/usr/local/lib/click.ko:/live/image/packages/package_name.ko"

b) Moreover, require(package_name); declaration should appear in Click's configuration. If you are using click_generator script to generate Click configuration, you should place appropriate instructions in function which generates headers:

    function generate_click_config_header()
    {
        printf("//\n");
        printf("// Generated by XORP FEA\n");
        printf("//\n");
        printf("\n\n");
        printf("require(package_name);");
        printf("\n\n");
    }


Download
--------

[Download ISO image](https://mega.co.nz/#!OEIjhQhA!Cs1t5nstf0xzClakUT8vJwUcX5YSaLE4IzRzW0JPAr0)


Web access
----------

By default, AGH Live Router starts HTTP server in the /click filesystem directory. You can easily access handlers through a web browser, gather data from click elements and modify its settings.


Credentials
-----------

Default user:

    login:router
    password:router
    
Root:

    login:root
    password:root


Screenshots
-----------

![1](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/1.png)
![2](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/2.png)
![3](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/3.png)
![4](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/4.png)
![5](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/5.png)
![6](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/6.png)
![7](https://raw.github.com/piotrjurkiewicz/agh-live-router/master/screenshots/7.png)
