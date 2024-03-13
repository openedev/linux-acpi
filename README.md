# ACPI vs UEFI

ACPI (Advanced Configuration and Power Interface) and UEFI (Unified Extensible Firmware Interface) are both important standards in modern computer systems, but they serve different purposes.

ACPI is a power management and configuration interface specification for hardware and operating systems. It defines various aspects of system configuration, including power states, device enumeration, and resource allocation. ACPI is typically implemented in the firmware (BIOS or UEFI) of a computer system and provides a standardized way for the operating system to interact with hardware components.

UEFI, on the other hand, is a replacement for the traditional BIOS (Basic Input/Output System) firmware interface. It provides more advanced features than BIOS, including support for larger hard drives, faster boot times, a modular architecture, and support for modern security features like Secure Boot. UEFI serves as the interface between the operating system and the firmware, providing a standardized way for the OS to boot and interact with system firmware.

While ACPI and UEFI serve different purposes, they are often closely integrated in modern computer systems. UEFI firmware typically includes support for ACPI, allowing the operating system to leverage ACPI features during boot and runtime. ACPI tables, which contain information about the system's hardware configuration and capabilities, are often provided by the firmware during the boot process and used by the operating system to configure hardware and manage power states. So, in summary, ACPI and UEFI work together to provide a standardized and efficient interface between the operating system and the underlying hardware.

# ACPI vs Devicetree

ACPI (Advanced Configuration and Power Interface) and DeviceTree serve somewhat similar purposes in terms of providing hardware configuration information to the operating system, but they are used in different types of systems and have different underlying architectures.

ACPI is primarily used in x86 and x86-64 (PC) systems, as well as some ARM-based systems. It provides a standardized way for the operating system to discover and configure hardware components, manage power states, and perform other system management tasks. ACPI tables, which contain information about the system's hardware configuration and capabilities, are typically provided by the system's firmware (BIOS or UEFI) during boot and used by the operating system to interact with hardware.

DeviceTree, on the other hand, is primarily used in embedded systems and is especially prevalent in ARM-based systems, including many single-board computers like the Raspberry Pi. DeviceTree is a data structure that describes the hardware configuration of a system, including information about CPU cores, memory, peripherals, and buses. It is typically provided by the bootloader to the operating system and used to initialize hardware components and set up the system's hardware configuration.

While ACPI and DeviceTree serve similar purposes in terms of providing hardware configuration information to the operating system, they have different architectures and are used in different types of systems. ACPI is more common in traditional PC systems, while DeviceTree is more common in embedded systems and ARM-based platforms.

# Change ACPI tables via UEFI services in kernel

 While changing ACPI tables at runtime is generally not supported or recommended due to potential system instability, the UEFI firmware does provide services that the Linux kernel can use to interact with ACPI tables during the boot process.

During the boot process, the Linux kernel can request information from ACPI tables provided by the firmware. Additionally, the kernel can use UEFI runtime services to query and interact with ACPI tables and system hardware.

In some cases, the Linux kernel may provide mechanisms to override or modify certain ACPI tables or settings at boot time, especially for specific hardware configurations or to work around compatibility issues. These modifications are typically done through kernel parameters or drivers designed to interact with ACPI tables.

However, it's important to note that modifying ACPI tables at runtime carries potential risks and may lead to system instability or unexpected behavior. Therefore, it's typically done cautiously and only when absolutely necessary, such as to address specific hardware issues or enable certain features. Additionally, any changes made to ACPI tables or settings typically require a reboot of the system to take effect fully.

# Update ACPI table in kernel console via UEFI services 

Updating ACPI tables directly from the kernel console via UEFI services is not a common or typical practice. ACPI tables are provided by the system's firmware during the boot process and are typically static throughout the runtime of the system. While the UEFI firmware provides services that the kernel can use to interact with ACPI tables and system hardware, these services are typically used for querying information rather than modifying tables.

Modifying ACPI tables directly from the kernel console would require deep knowledge of the system's firmware and ACPI implementation, as well as custom software to handle the modifications safely. Making incorrect modifications to ACPI tables can lead to system instability or even hardware damage.

If you need to modify ACPI tables, it's typically done at the firmware level, either by updating the firmware itself or by configuring the firmware to provide different ACPI tables during the boot process. This would require rebooting the system to apply the changes.

Overall, modifying ACPI tables directly from the kernel console via UEFI services is not a recommended or common approach. It's best to handle ACPI table modifications through firmware updates or configuration changes, followed by a system reboot to ensure stability and proper functionality.

# What if I would require to change as I'm attaching graphic card in PCIE when I'm in Linux console and want this graphic card information must be appended to ACPI tables as Linux PCIe support hotplug ?

In scenarios where you're dynamically attaching hardware such as a graphics card via PCIe while the system is running, the ACPI tables won't be updated automatically to reflect these changes. ACPI tables are typically initialized during the boot process and don't dynamically update based on hardware hot-plugging.

However, the Linux kernel does have mechanisms to handle hot-plugged devices, including PCIe devices like graphics cards. When you hot-plug a PCIe device, the kernel detects the new device and configures it accordingly, typically through the ACPI or PCIe hotplug mechanisms.

In the case of a graphics card, the kernel will detect the new device and load the appropriate drivers, initializing it for use. While the ACPI tables won't be updated to reflect the hot-plugged device, the kernel will manage the device dynamically, allowing you to use it without needing to modify ACPI tables directly.

If you specifically require the hot-plugged device information to be reflected in ACPI tables, you may need to develop custom software or scripts to handle this. This could involve querying the kernel for information about the hot-plugged device and updating ACPI tables accordingly. However, this approach would be complex and potentially risky, as ACPI tables are critical system configuration data, and modifying them incorrectly could lead to system instability or hardware damage.

In most cases, it's best to rely on the kernel's built-in mechanisms for handling hot-plugged devices and manage them dynamically without modifying ACPI tables directly.

Modifying ACPI tables at runtime is not a standard practice due to the complexity and potential risks involved. However, I can provide some general steps that you might take if you were to attempt this, but please be aware that these steps are highly advanced and should only be undertaken by experienced developers with a deep understanding of system internals.

Here are the general steps you might follow:

- Identify the ACPI tables: Use tools like acpidump or dmidecode to identify the ACPI tables present in your system. You can also explore the /sys/firmware/acpi/tables directory in Linux to see the ACPI tables that are currently loaded.
-Extract the ACPI tables: Use acpidump or similar tools to extract the ACPI tables to files that you can modify. For example:
```
sudo acpidump > acpi_tables.dat
```
- Decompile the ACPI tables: Use an ACPI table decompiler like iasl to decompile the ACPI tables into human-readable ASL (ACPI Source Language) code:
```
iasl -d acpi_tables.dat
```
- Modify the ACPI tables: Edit the decompiled ACPI code to make the desired changes. This could involve adding, removing, or modifying ACPI objects and fields.
- Compile the modified ACPI tables: Once you've made your changes, compile the modified ACPI code back into binary ACPI tables:
```
iasl -tc modified.asl
```
- Load the modified ACPI tables: Use the acpi_override kernel parameter or a similar mechanism to load the modified ACPI tables at boot time. This typically involves placing the modified ACPI tables in the /boot directory and configuring your bootloader (e.g., GRUB) to load them.
- Reboot the system: Reboot the system to apply the modified ACPI tables. This step is necessary because ACPI tables are loaded during the boot process and cannot be modified at runtime without potentially causing system instability.

# How to find the pluges graphic card tables.?

To find ACPI tables related to your plugged-in graphics card, you can use tools like acpidump or acpiexec to extract and examine ACPI tables. These tools can provide detailed information about the ACPI tables present in your system, including those related to your graphics card.

Here's how you can use acpidump to extract ACPI tables and then search for tables related to your graphics card:

- First, install the acpidump tool if it's not already installed on your system. You can typically find it in your distribution's package repository. For example, on Ubuntu, you can install it with the following command:
```
sudo apt-get install acpidump
```
- Once acpidump is installed, you can use it to extract ACPI tables to a file. Run the following command to dump ACPI tables to a file named acpi_tables.dat:
```
sudo acpidump > acpi_tables.dat
```
- Now, you can search for tables related to your graphics card in the extracted ACPI tables file. You can use tools like grep to search for specific strings or patterns within the file. For example, you might search for strings related to your graphics card manufacturer or model:
```
grep -i "NVIDIA" acpi_tables.dat
```
Replace "NVIDIA" with the name of your graphics card manufacturer or model. This command will search for any occurrences of the string "NVIDIA" in the ACPI tables file.
- Additionally, you can use acpixtract to extract individual ACPI tables from the acpi_tables.dat file for further examination. For example, to extract a specific table, you can run:
```
acpixtract -a acpi_tables.dat
```
This command will extract all ACPI tables from the acpi_tables.dat file.

# Typically ACPI tables tree for PCI root complex and slaves

The ACPI tables related to the PCI root complex and its devices are typically organized in the ACPI namespace, which represents the hardware configuration of the system. ACPI tables related to PCI devices are usually found under the System Resource Affinity Table (SRAT) and the Multiple APIC Description Table (MADT).

Here's a typical hierarchy of ACPI tables related to PCI devices:

- System Resource Affinity Table (SRAT): The SRAT provides information about the system's NUMA (Non-Uniform Memory Access) topology, including memory affinity and proximity domains. While it doesn't directly describe PCI devices, it provides context for understanding the system's memory and device layout.
- Multiple APIC Description Table (MADT): The MADT describes the system's interrupt controllers and their relationships. It includes information about the APIC (Advanced Programmable Interrupt Controller) structures and their association with devices, including PCI devices.
- _PRT (PCI Routing Table): The PCI Routing Table (_PRT) is an ACPI table that describes the interrupt routing for PCI devices. It provides information about which interrupt pins of PCI devices are connected to which interrupt controllers (typically APICs) and how interrupt signals are routed.
- SB (System Bus) and _BBN (Bus Number) objects: These ACPI objects represent the PCI root complex and its associated buses. They provide information about the PCI hierarchy, including the root bus and any subordinate buses. Information about PCI devices connected to these buses may also be found under these objects.
- _ADR (Address) objects: These ACPI objects represent PCI devices and their addresses within the system's address space. Each PCI device typically has an _ADR object that specifies its address range.
- _CRS (Current Resource Settings) objects: These ACPI objects describe the current resource allocation settings for PCI devices, including memory ranges, I/O ports, and interrupt assignments.

These are some of the key ACPI tables and objects related to PCI devices in the ACPI namespace. The actual structure and contents of ACPI tables may vary depending on the system's hardware configuration and firmware implementation. To explore the ACPI namespace and view the ACPI tables on your system, you can use tools like acpidump or examine the /sys/firmware/acpi/namespace directory in Linux.
