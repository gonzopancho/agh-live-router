[AGH Live Router](http://piotrjurkiewicz.github.com/agh-live-router/)
=================

AGH Live Router is a Debian derivative distribtion for experimental routing. It consists of Click Modular Router, running in kernel mode, and XORP routing suite. System is already configured and ready-to-use, distributed as an .iso image.

Find out more in [presentation](http://prezi.com/iez6ctgkd8dt/agh-live-router/).


Details
=======

ISO file contains image of a live system. Distribution is based on Debian 6.0 (Squeeze). It contains installed Click environment (linuxmodule version) and XORP suite. XORP starts automatically on system startup. XORP, during its initialization, loads Click module. Therefore, router becomes operational automatically after system startup. Additionally, along with start of graphical environment, Clicky application starts. This allows fast verification of correctness of Click startup and configuration load.


Usage
=====

Bootable live USB drive can be created with the [UNetbootin](http://unetbootin.sourceforge.net/) application. After creation of USB drive, you may want to put router configuration files on the USB. The configuration will be automatically loaded on system startup. Configuration files should be placed in "config" directory on the USB drive. This directory must be created manually. The directory should contain the following files:

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

The click_generator file contains an AWK script, which generates the kernel-level Click configuration from the XORP configuration. The script is called on-demand by the FEA whenever the network interface information changes. The default script you can find [there](https://github.com/piotrjurkiewicz/agh-live-router/blob/master/configurations/simple/config/click_generator). You may not want to modify generator script, unless you are making significant changes in router's functioning.


Download
========

[Download ISO image](http://pluton.kt.agh.edu.pl/~pjurkiewicz/agh-live-router.iso)


Credentials
===========

Default user:

    login:router
    password:router
    
Root:

    login:root
    password:root  

