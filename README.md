# A Jounety to the Hackintosh Land
---
The purpose of this repo is to provide a starter to get MacOS running on the recent HP Laptop model.

> Specs:
```mdblock
|[CPU][i7-7700HQ ] - No iGPU, disable by default
|[Mother Board][Intel HM175 ] - Custom BIOS
|[GPU][NVIDIA GeForce GTX 1060 Max-Q - 6144 MB, Core: 1063 MHz, Memory: 8000 MHz, GDDR5, ForceWare 387.92]
|[RAM][16384 MB, PC4-21300, 8GB * 2]
|[NVMe-SSD][SAMSUNG MZVLW256HEHP-000H1, 256 GB]
|[SSD][CT250BX100SSD1, 256GB]
|[Soundcard][Intel Corporation CM238 HD Audio Controller]
|[WiFi+ BT][Realtek RTL8169/8110 (10/100MBit), Realtek 8822BE Wireless LAN 802.11ac PCI-E NIC (a/b/g/n/ac), Bluetooth 4.2] --> Swap to [Dell Wireless 1830 Bluetooth 4.1 LE][BCM920703]
|[Display][15.6 inch 16:9, 1920 x 1080 pixel 141 PPI, AUO42ED, IPS, Full HD, 120 Hz, G-Sync, glossy: no]
|[Keyboard][Japanese Standard Keyboard (JIS)]
```

> Want more specs? [lscpi.txt](lspci.txt), this is the lscpi from the original laptop, many things have been changed to make it working.

## ToC
---
> UPDATING

## Preface
---
So if you haven't noticed the specs of the laptop. You will probably guess, this laptop looks a lot like a PC. However, a lot of the post-installation is by following Rehabman Guide on TonymacX86. The pre-installation is by following /r/hackintosh. So please check the guide based on that.

> Note about the laptop model:
- Some of the high-end laptop disable iGPU be default, in order to run a higher refresh rate Display Panel. So If you coming from a low-end model HP Omen laptop, please reading and following the Rehabman Laptop Guide. E.g: GTX 1050ti or lower will be using iGPU.
- During pre-installation, things should be different based on model. But for the post-installation, i have been helping other people to get stuffs working, and it is basically the same process.

### Things that work:
- [x] Nvidia GPU.
- [x] 120hz-display, no backlight.
- [x] Keyboard + Trackpad (partial).
- [x] Ports: HDMI, USB3/2, Thunderbolt 3 (plug before booting)
- [x] Wifi + Bluetooth features.
- [x] Battery.
- [x] Power management.
- [x] Webcam
- [x] Audio
- [x] Sleep
- [x] SDCard

### Things that not:
- [] HDMI Audio
- [] Backlight
- [] DisplayPort (i don't have the resources so haven't tested it yet)
- [] Thunderbolt3 (i don't have the resources so haven't tested it yet)

### Things to remember:
- [] The 1TB HDD that come with the Laptop is terrible, it is often heat up to 45 degree, so you should swap it out. Or apply some tweaks that will not be covered in this repos (yet).

## Requirements
---
- A Working MacOS.
  > VMWare is fine but you should finish booting into macosx with the VMware USB. Once when you boot into macosx, and do a clean install again. 

- USB3/USB2 8GB or more.
- 40$ for the Wifi. Ignore if that is not what you are looking forward to have.
- Reading, and don't expect to be spoonfed.

## Guide
---
> As i was saying before, but this guide is not for laptop with iGPU enabled. If anyone have a different laptop model that got it working, please feel free to add it here. I will try to update to cover as many as possible.

### Making the Installer
> Sample [config.plist](CLOVER/EFI/CLOVER/config.plist), as this is my final config.plist to make everything working, so you should disable most of thems for the first installation process.

#### Essential kexts:
├── FakeSMC.kext // You will needs this no matter what
├── Lilu.kext // Go with NvidiaGraphicsFixup.kext
├── NvidiaGraphicsFixup.kext // For VESA inorder to boot into the installation
├── RealtekRTL8111.kext // Ethernext for Realtek8111
├── SATA-100-series-unsupported.kext // Not regconized the partition so you will need this.
├── USBInjectAll.kext // Using with port limit patch.
├── VoodooPS2Controller.kext // For working Keyboard and Mouse
├── XHCI-200-series-injector.kext // Go with USBInjectAll.kext

#### Optional Kexts please checking with the [Kexts.txt](Kexts.txt)


#### CLOVER CONFIGURATOR (CC)
##### ACPI
- change SAT0 to SATA
- change HDAS to HDEF (For working Audio, you can disabled it for now)
- change HECI to IMEI
- change MEI to IMEI
- change OSID to XSID (to avoid match against _OSI XOSI patch)
- change _OSI to XOSI

###### Fixes: FixShutdown (use if you restart when clicked shutdown), fixHPET, Add MCHC, FixIPIC, FixSBUS.
###### Enable AutoMerge, FixHeaders.
###### Drop Tables: DMAR, MATS.
###### SSDT, some how when using PikeGenssdt.sh i got over heating and poor results in benchmark, so sticking with Clover is my solution for now, will look into it in the future.

##### Essential KextsToPatch: (If you have already bought the wifi, i think it will work out of the box without any KextsToPatch. But you should consider disable and enbale the one you need.

If you have a compatible wifi, or using the same one as mine.
```plist
<key>KernelToPatch</key>
<array>
	<dict>
		<key>Comment</key>
		<string>MSR 0xE2 _xcpm_idle instant reboot(c) Pike R. Alpha</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		ILniAAAADzA=
		</data>
		<key>Replace</key>
		<data>
		ILniAAAAkJA=
		</data>
	</dict>
</array>
<key>KextsToPatch</key>
<array>
	<dict>
		<key>Comment</key>
		<string>10.13-BCM94352-fvco-darkvoid-Sherlocks</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		gflSqgAAdL8=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		gflSqgAAZpA=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.13-BCM94352-iovar-Sherlocks</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		hdtMi2XIeXE=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		hdtMi2XI63E=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.13-BCM94352-iovar-Sherlocks</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		SIPEEInDhdt5Rg==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		SIPEEInDhdvrRg==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.13-BCM94352-CC-Sebinouse-(edit 55 53 to CC)-PMheart</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		QYP8/3Q1SI1V0A==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		ZscGVVPrNI1V0A==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.13-BCM94352-Airport-Extreme-Skvo-Sherlocks</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		axAAAHUN
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		axAAAJCQ
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.13-Disable-Whitelist-check-RehabMan</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		MdtMy2XYdRI=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.BrcmNIC-MFG</string>
		<key>Replace</key>
		<data>
		Mdv/w5CQkJA=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.12+-BCM94352-fvco-darkvoid</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		gflSqgAAdSk=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		gflSqgAAZpA=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.10-10.11-BCM94352-5GHz-US-FCC-darkvoid</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		QYP8/3QsSA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.10.x,10.11.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		ZscGVVPrKw==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.9-BCM94352-5 GHz - Skvo</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		AVhU
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.9.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		AVhY
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.11+-BCM94352-CC-Sebinouse-(edit 55 53 to CC)</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		QYP8/3QsSA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		ZscGVVPrKw==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.11+-BT4LE-Handoff-Hotspot-lisai9093</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		SIX/dEdIiwc=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x,10.13.x</string>
		<key>Name</key>
		<string>com.apple.iokit.IOBluetoothFamily</string>
		<key>Replace</key>
		<data>
		Qb4PAAAA60Q=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.10.2+-BT4LE-Handoff-Hotspot-Dokterdok</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		SIXAdFwPt0g=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.10.2,10.10.3,10.10.4,10.10.5</string>
		<key>Name</key>
		<string>com.apple.iokit.IOBluetoothFamily
&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
&lt;plist version="1.0"&gt;
&lt;string&gt;com.apple.iokit.IOBluetoothFamily&lt;/string&gt;
&lt;/plist&gt;
</string>
		<key>Replace</key>
		<data>
		Qb4PAAAA61k=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.10-10.10.1-BT4LE-Handoff-Hotspot-Dokterdok</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		i4eMAQAA
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.10,10.10.1</string>
		<key>Name</key>
		<string>com.apple.iokit.IOBluetoothFamily</string>
		<key>Replace</key>
		<data>
		uA8AAACQ
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.10+-BCM94352-Airport-Extreme-Skvo</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		axAAAHUN
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.10.x,10.11.x,10.12.x,10.13.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		axAAAJCQ
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.11+-Disable-Whitelist-check-RehabMan</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		MdtMy2XYdRI=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		Mdv/w5CQkJA=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.11+-BCM94352-CC=XT-Skvo</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		WFQAKxgsIQAg
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		WFQAP0hQXAAK
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>10.11+-BCM94352-CC=#a-Ramalama</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		QYP8/3QsSA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.11.x,10.12.x</string>
		<key>Name</key>
		<string>com.apple.driver.AirPort.Brcm4360</string>
		<key>Replace</key>
		<data>
		ZscGI2HrKw==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>Change 15 port limit to 24 in XHCI kext 10.13 PB1</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		g32MEA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>AppleUSBXHCIPCI</string>
		<key>Replace</key>
		<data>
		g32MGw==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>change 15 port limit to 26 in XHCI kext (credit FredWst)</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		g32UDw+DlwQ=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.4,10.13.5</string>
		<key>Name</key>
		<string>com.apple.driver.usb.AppleUSBXHCI</string>
		<key>Replace</key>
		<data>
		g32UGg+DlwQ=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>disable port limit in XHCI kext (credit PMHeart)</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		g32UDw+DlwQAAA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.4,10.13.5</string>
		<key>Name</key>
		<string>com.apple.driver.usb.AppleUSBXHCI</string>
		<key>Replace</key>
		<data>
		g32UD5CQkJCQkA==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>change 15 port limit to 26 in XHCI kext</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		g32MEA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>MatchOS</key>
		<string>10.13.0,10.13.1,10.13.2,10.13.3</string>
		<key>Name</key>
		<string>com.apple.driver.usb.AppleUSBXHCIPCI</string>
		<key>Replace</key>
		<data>
		g32MGw==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>change F%uT%04x to F%uTxxxx in AppleBacklightInjector.kext (credit RehabMan)</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		RiV1VCUwNHgA
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>com.apple.driver.AppleBacklight</string>
		<key>Replace</key>
		<data>
		RiV1VHh4eHgA
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>0x591b0000, 32MB BIOS, 19MB framebuffer 9MB cursor bytes (credit RehabMan)</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		AABgAgAAUAE=
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>com.apple.driver.AppleIntelKBLGraphicsFramebuffer</string>
		<key>Replace</key>
		<data>
		AAAwAQAAkAA=
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>0x591b0000, 0105 instead of 0306, HDMI</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		AgQKAAAIAACHAQAAAwYKAAAEAACHAQAA/wAAAAEAAAAg
		AAAA
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>com.apple.driver.AppleIntelKBLGraphicsFramebuffer</string>
		<key>Replace</key>
		<data>
		AQUKAAAIAACHAQAAAgQKAAAIAACHAQAA/wAAAAEAAAAg
		AAAA
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>0x591b0000, 0105 instead of 0204, HDMI</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		AgQKAAAIAACHAQAAAwYKAAAEAACHAQAA/wAAAAEAAAAg
		AAAA
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>com.apple.driver.AppleIntelKBLGraphicsFramebuffer</string>
		<key>Replace</key>
		<data>
		AQUKAAAIAACHAQAAAwYKAAAEAACHAQAA/wAAAAEAAAAg
		AAAA
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>0x591b0000, eliminate all external ports (0204 and 0306)</string>
		<key>Disabled</key>
		<true/>
		<key>Find</key>
		<data>
		AgQKAAAIAACHAQAAAwYKAAAEAACHAQAA/wAAAAEAAAAg
		AAAA
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>com.apple.driver.AppleIntelKBLGraphicsFramebuffer</string>
		<key>Replace</key>
		<data>
		/wAAAAEAAAAgAAAA/wAAAAEAAAAgAAAA/wAAAAEAAAAg
		AAAA
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>Zeroing 10ec0885 codec (patched by AppleHDA Patcher.app)</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		hQjsEA==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>AppleHDA</string>
		<key>Replace</key>
		<data>
		AAAAAA==
		</data>
	</dict>
	<dict>
		<key>Comment</key>
		<string>Patching 11d41984 with 10ec0295 codec (patched by AppleHDA Patcher.app)</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		hBnUEQ==
		</data>
		<key>InfoPlistPatch</key>
		<false/>
		<key>Name</key>
		<string>AppleHDA</string>
		<key>Replace</key>
		<data>
		lQLsEA==
		</data>
	</dict>
</array>
</dict>
```
### Post-installation

## Tips & Tricks
---
* EC/CMOS Reset
  * If the battery is unplugable, just do that and hold down the power button for 15s. Or else you should find the correct way to do a EC/CMOS Reset, because you need it for the battery.
* Battery Tricks
  * abm_firstpolldelay=8000/16000.
  * You should take the battery out when you installing ACPIBatteryManager.kext. Put the battery back when done.

## Contributing
---

## Credits
- Rehabman guide.
- /r/Hackintosh.
- Hackintosh Discord Server.

