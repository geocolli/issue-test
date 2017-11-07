# Harmony: VMware Review

Please add questions that you have regarding VMware setup, configuration, etc.

*   How do you setup a dhcp server for VM?
    *   shmcfarl: Create a standard VM (linux or Windows) and place it on the same network as the VMs you want to have DHCP do offers. Install your favorite DHCP service (DNSMASQ, Windows DHCP server).  Assign the DHCP scope that matches the subnet/prefix. Have a nice day.
    *   Here are instructions on deploying DHCP server on CentOS: [https://www.tecmint.com/install-dhcp-server-in-centos-rhel-fedora/](https://www.tecmint.com/install-dhcp-server-in-centos-rhel-fedora/) (The instructions are pretty much the same on every site)
*   Best practices for building an OVA
    *   shmcfarl: Build your VM the way you want it to include BIOS settings (select "Enabled advanced options" in the wizard). Shut down the VM. Right-click on the VM > Template > "Export OVF Template". Follow the steps.  
    *   ![](/images/194576442/194576448.png)
    *   Note: In vSphere 6.5 the option to select the OVA format as an export option is gone (they had horrid issues with that feature). So, per the release notes ([https://docs.vmware.com/en/VMware-vSphere/6.5/rn/vsphere-esxi-vcenter-server-65-release-notes.html](https://docs.vmware.com/en/VMware-vSphere/6.5/rn/vsphere-esxi-vcenter-server-65-release-notes.html)), you can follow these instructions:
        *   **vSphere Web Client does not support exporting virtual machines or vApps as OVA templates**
            In versions earlier than vSphere 6.5, you could export virtual machines and vApps as an OVA template on the vSphere Web Client. This functionality is not available in vSphere 6.5.

            Workaround: Export the virtual machine as an OVF template, and then create an OVA template from the OVF template files. The following procedure describes this process using the Linux an Mac commands. Windows systems require installation of a TAR capable utility.

            1.  Use the vSphere Web Client to export the VM or vApp as OVF template to the local machine.

            2.  Locate the downloaded OVF template files, and move them into an empty new folder.

            3.  Perform one of the following tasks to create an OVA template from an OVF template.

                *   Go to the new folder and create an OVA template using the tar command to combine the files:
                    `cd<span> </span>_folder_
                    tar cvf<span> </span>_ova-template-name_.ova<span> </span>_ovf-template-name_.ovf
                    tar uvf<span> </span>_ova-template-name_.ova<span> </span>_ovf-template-name_.mf
                    tar uvf<span> </span>_ova-template-name_.ova<span> </span>_ovf-template-name_-1.vmdk
                    ...
                    tar uvf<span> </span>_ova-template-name_.ova<span> </span>_ovf-template-name_-_n_.vmdk`

                    _n_<span> </span>refers to the number of disks the VM contains.<span> </span>_ova-template-name_.ova is the final OVA template. Run the commands in the exact order so the OVA is correctly built.

                    Note: The<span> </span>`tar`<span> </span>command must use the TAR format and comply with the USTAR (Uniform Standard Tape Archive) format as defined by the POSIX IEEE 1003.1 standards group.

                *   If the OVF tool is installed on your system, run the following command:
                    `cd<span> </span>_downloaded-ovf-template-folder_
                    _path-to-ovf-tool_\ovftool.exe<span> </span>_ovf-template-name_.ovf<span> </span>_ova-template-name_.ova`

### Best practices and documentation links:

*   vSphere documentation: [https://docs.vmware.com/en/VMware-vSphere/index.html](https://docs.vmware.com/en/VMware-vSphere/index.html) 
*   vSphere 6.5 Network documentation: [https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf) 
*   DVS best practices: [https://blogs.vmware.com/vsphere/2011/11/vds-best-practices.html](https://blogs.vmware.com/vsphere/2011/11/vds-best-practices.html)
*   vSphere 6.5 Performance Best Practices: [https://www.vmware.com/techpapers/2017/Perf_Best_Practices_vSphere65.html](https://www.vmware.com/techpapers/2017/Perf_Best_Practices_vSphere65.html)
*   Content Libraries: [https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-254B2CE8-20A8-43F0-90E8-3F6776C2C896.html](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-254B2CE8-20A8-43F0-90E8-3F6776C2C896.html) 

### Meeting Recordings: 
**9/26/2017 Meeting with Shannon McFarland and Christie Hicks:**

[**Play recording**](https://cisco.webex.com/ciscosales/lsr.php?RCID=e7e13c2d7b744bba97978d92ddc14496) (39 min 43 sec)
Recording password: Ph8XZPuJ