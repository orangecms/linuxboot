default: build

build:
	echo fetch, uroot, flashkernel, or image.bin

image.bin: flashkernel
	utk \
		  -xzPath  /usr/bin/xz \
		sr630.bin \
	        remove Ip.*  \
                remove Tcp.*  \
                remove Usb.*  \
                remove Udp.*  \
                remove Dhcp.*  \
                remove .np.*  \
                remove .tftp.*  \
                remove Http.* \
                remove .*Dns.* \
                remove Arp.* \
                remove .*NetworkStackSetupScreen.*  \
                remove Iscsi.* \
                remove Scsi.* \
                remove Fat.* \
                remove Ahci.* \
                remove Partition.* \
                remove Sata.* \
                remove Disk.* \
                remove Whea.* \
                remove .*Pxe.* \
                remove Ata.* \
		remove AMITSE \
		replace_pe32 FullShell flashkernel  \
	save image.bin

flashkernel: uroot bzImage
	cp linux/arch/x86/boot/bzImage flashkernel

readrom:
	echo note: for this to work you need to plug in power
	echo also .... flashrom will not work you need sf100 on windows
	sudo flashrom -p dediprog -r sr630.bin

writerom: image.bin
	sudo flashrom -p dediprog -w sr630.bin

uroot:
	go run github.com/u-root/u-root -build=bb github.com/u-root/u-root/cmds/core/init github.com/u-root/u-root/cmds/exp/rush github.com/u-root/u-root/cmds/core/ls github.com/u-root/u-root/cmds/core/ip   github.com/u-root/u-root/cmds/core/elvish    github.com/u-root/u-root/cmds/core/sshd    github.com/u-root/u-root/cmds/core/scp
	cp /tmp/initramfs.linux_amd64.cpio .
	lzma -k -f initramfs.linux_amd64.cpio
	ls -l initramfs*
	cp *lzma linux

bzImage:
	cp config-4.14.111 linux/.config
	(cd linux && make oldconfig && make -j32)

fetch: getkernel geturoot getfiano getrom
getkernel:
	rm -rf linux
	wget -O kernel.xz https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.14.111.tar.xz
	xzcat kernel.xz | tar x
	mv linux-4.14.111 linux
getfiano:
	go get -u github.com/linuxboot/fiano/cmds/utk
	go install github.com/linuxboot/fiano/cmds/utk
getrom:
	echo you can put a wget here
	echo and unxip it 
	echo and cp it to sr630.bin
geturoot:
	go get -u github.com/u-root/u-root

# everything from here below was part of an experiment and can be ignored.
experiment:
	echo b to build, i for initramfs, q for qemu, d to dxeclean

b: buildbzimage

d:
	ForthUTK sr630.bin fv 'AMITSE|Ata.*|Disk.*|.*Ftp.*|.*Dhcp.*|Tcp.*|.*Pxe.*|.*Udp.*' ix r r tag  /tmp/x saverom /tmp/x fv linux/arch/x86/boot/bzImage splat drop /tmp/x saverom	./DXECLEANER clean

d98:
	ForthUTK 98.splat fv ./DXECLEANER clean

d4:
	ForthUTK RUN4.4.rom fv ./DXECLEANER clean

buildbzimage:
	(cd linux && make -j32 && ls -l arch/x86/boot/bzImage)
	
i:
	(cd ~/go/src/github.com/linuxboot/dut/uinit/&&go build)
	cp ~/go/src/github.com/linuxboot/dut/uinit/uinit .
	go run $(GOPATH)/src/github.com/u-root/u-root -build=bb -files id_rsa.pub github.com/linuxboot/dut/uinit github.com/u-root/u-root/cmds/init github.com/u-root/u-root/xcmds/rush github.com/u-root/u-root/cmds/ls github.com/u-root/u-root/cmds/ip   github.com/u-root/u-root/cmds/elvish    github.com/u-root/u-root/xcmds/sshd    github.com/u-root/u-root/cmds/scp
	cp /tmp/initramfs.linux_amd64.cpio .
	lzma -k -f initramfs.linux_amd64.cpio
	ls -l initramfs*
	cp *lzma linux

burn: bigi b
	ForthUTK remove100.rom fv linux/arch/x86/boot/bzImage splat remove100big.rom saverom  < /dev/null
	./BURN remove100big.rom

bigi:
	(cd ~/go/src/github.com/linuxboot/dut/uinit/&&go build)
	cp ~/go/src/github.com/linuxboot/dut/uinit/uinit .
	go run $(GOPATH)/src/github.com/u-root/u-root -build=bb -files key.pub minimal  github.com/linuxboot/dut/uinit   github.com/u-root/u-root/xcmds/sshd github.com/u-root/u-root/xcmds/pox   github.com/u-root/u-root/cmds/scp   github.com/u-root/u-root/cmds/cpio
	cp /tmp/initramfs.linux_amd64.cpio .
	lzma -k -f initramfs.linux_amd64.cpio
	ls -l initramfs*
	cp *lzma linux

cleanme:
	utk dxeclean.bin dxecleaner_blacklist ./DXECLEANER ./blacklist

q:
	/usr/bin/qemu-system-x86_64 -kernel linux/arch/x86/boot/bzImage  -serial stdio -monitor /dev/null -m 8192 -nographic
findit:
	jq -c 'to_entries[] | .value | select(.Type == "EFI_FV_FILETYPE_DRIVER") | .Sections [] | .Encapsulated ' < xxx
	jq -c 'to_entries[] | .value | select(.Type == "EFI_FV_FILETYPE_DRIVER") | .Sections [] | .Encapsulated[1] | [.Value.Name]'< xxx

all:
	utk sr630.bin \
		remove Ip.*  \
		remove Tcp.*  \
		remove Usb.*  \
		remove Udp.*  \
		remove Dhcp.*  \
		remove .np.*  \
		remove .tftp.*  \
		remove Http.* \
		remove .*Dns.* \
		remove Arp.* \
		remove .*NetworkStackSetupScreen.*  \
		remove Iscsi.* \
		remove Scsi.* \
		remove Fat.* \
		remove Ahci.* \
		remove Partition.* \
		remove Sata.* \
		remove Disk.* \
		remove Whea.* \
		remove .*Pxe.* \
		remove Ata.* \
		replace_pe32 FullShell bzImage \
		save x.bin \
		remove .*Ipmi.* \
		save ipmisucks.bin \
		remove DxeIpmiBmcInitialize \
		remove SmmBmcElog \
		remove BmcAcpi \
		remove SmmIpmiBmcInitialize \
		remove BmcElog \
		remove BmcLanConfig \
		save removeBMCcrap.bin \
		remove SecurityStubDxe \
		save removesomecrap.bin \
		comment TimestampDxe \
		comment Legacy8259 \
		comment HiiDatabase \
		comment DataHubDxe \
		comment EnglishDxe \
		comment here \
		comment PcRtc \
		comment FpgaDxe \
		comment LegacyInterrupt \
		comment SmartTimer \
		comment LegacySredir \
		comment LegacyRegion2 \
		comment TcgLegacy \
		comment LegacySmmSredir \
		comment DevicePathDxe \
		comment CpuIo2Dxe \
		comment CpuMpDxe \
		comment SmmAccess \
		comment WdtDxe \
		save commentmorecrap.bin \
		comment SetupConfigUpdateDxeNeonCityEPRP \
		comment StaticSkuDataDxeNeonCityEPRP \
		comment OpromUpdateDxeNeonCityEPRP \
		comment SmbiosDataUpdateDxeNeonCityEPRP \
		comment IioCfgUpdateDxeNeonCityEPRP \
		comment SlotDataUpdateDxeNeonCityEPRP \
		comment SetupConfigUpdateDxeLightningRidgeEXRP \
		comment StaticSkuDataDxeLightningRidgeEXRP \
		comment OpromUpdateDxeLightningRidgeEXRP \
		comment SmbiosDataUpdateDxeLightningRidgeEXRP \
		comment IioCfgUpdateDxeLightningRidgeEXRP \
		comment SlotDataUpdateDxeLightningRidgeEXRP \
		comment OpromUpdateDxeNeonCityFPGA \
		comment SetupConfigUpdateDxeNeonCityFPGA \
		comment SmbiosDataUpdateDxeNeonCityFPGA \
		comment StaticSkuDataDxeNeonCityFPGA \
		comment IioCfgUpdateDxeNeonCityFPGA \
		comment SlotDataUpdateDxeNeonCityFPGA \
		comment FpgaConfigDataDxeNeonCityFPGA \
		comment IioCfgUpdateDxeLightningRidgeEXECB1 \
		comment OpromUpdateDxeLightningRidgeEXECB1 \
		comment SetupConfigUpdateDxeLightningRidgeEXECB1 \
		comment SlotDataUpdateDxeLightningRidgeEXECB1 \
		comment SmbiosDataUpdateDxeLightningRidgeEXECB1 \
		comment StaticSkuDataDxeLightningRidgeEXECB1 \
		comment IioCfgUpdateDxeLightningRidgeEXECB2 \
		comment OpromUpdateDxeLightningRidgeEXECB2 \
		comment SetupConfigUpdateDxeLightningRidgeEXECB2 \
		comment SlotDataUpdateDxeLightningRidgeEXECB2 \
		comment SmbiosDataUpdateDxeLightningRidgeEXECB2 \
		comment StaticSkuDataDxeLightningRidgeEXECB2 \
		comment IioCfgUpdateDxeLightningRidgeEXECB3 \
		comment OpromUpdateDxeLightningRidgeEXECB3 \
		comment SetupConfigUpdateDxeLightningRidgeEXECB3 \
		comment SlotDataUpdateDxeLightningRidgeEXECB3 \
		comment SmbiosDataUpdateDxeLightningRidgeEXECB3 \
		comment StaticSkuDataDxeLightningRidgeEXECB3 \
		comment IioCfgUpdateDxeLightningRidgeEXECB4 \
		comment OpromUpdateDxeLightningRidgeEXECB4 \
		comment SetupConfigUpdateDxeLightningRidgeEXECB4 \
		comment SlotDataUpdateDxeLightningRidgeEXECB4 \
		comment SmbiosDataUpdateDxeLightningRidgeEXECB4 \
		comment StaticSkuDataDxeLightningRidgeEXECB4 \
		comment IioCfgUpdateDxeNeonCityEPECB \
		comment OpromUpdateDxeNeonCityEPECB \
		comment SetupConfigUpdateDxeNeonCityEPECB \
		comment SlotDataUpdateDxeNeonCityEPECB \
		comment SmbiosDataUpdateDxeNeonCityEPECB \
		comment StaticSkuDataDxeNeonCityEPECB \
		save commentsetupcrap.bin \
		comment PchSerialGpio \
		comment PchSmbusDxe \
		comment IioInit \
		comment PpmInitialize \
		comment CpuArchDxe \
		comment PchInitDxe \
		comment start_dxecleaner \
		remove AB7ED12E-1D78-4635-AB87-23F00A911EC7 \
		save fail.bin \
		comment HeciInitDxe \
		comment SpsDxe \
		comment SpsSmm \
		comment Aint13 \
		comment Setup \
		comment ServerMgmtSetup \
		comment ACPI \
		comment S3SaveStateDxe \
		comment BootScriptExecutorDxe \
		comment SioDxeInit \
		comment OA3 \
		comment OptionRomPolicy \
		comment PciRootBridge \
		comment PciDxeInit \
		comment PciOutOfResourceSetupPage \
		comment RuntimeMemoryHoleVar \
		comment ExportHiiDb \
		comment DpcDxe \
		comment AmiBoardInfo2 \
		comment ReFlash \
		comment CsmDxe \
		comment UbaConfigDatabaseDxe \
		comment HardwareSignatureEntry \
		comment KbcEmulDxe \
		comment EsrtDxe \
		comment NvmeInt13 \
		comment OpalSecurity \
		comment AcpiPlatform \
		comment AcpiVTD \
		comment PlatformCpuPolicy \
		comment PciPlatform \
		comment PlatformEarlyDxe \
		comment PlatformType \
		comment S3NvramSave \
		comment UuidDxe \
		comment OpaPlatCfg \
		comment MemorySubClass \
		comment SocketSetup \
		comment FpgaSocketSetup \
		comment HstiPlatformDxe \
		comment HstiIhvProviderDxe \
		comment IsPlatformSupportWhea \
		comment OemVtdRmrr \
		comment MePolicyInitDxe \
		comment SpsAcpiHooks \
		comment Platform \
		comment FpkSetup \
		comment SystemBoardDxe \
		comment UbaInitDxe \
		comment CsmRt32 \
		comment AmiRedFishApi \
		comment AmiDeviceGuardApi \
		comment TxtDxe \
		comment Smbios \
		comment SmbiosBoard \
		comment SmbiosGetFlashData \
		comment AmiTcgNvflagSample \
		comment AmiTcgPlatformDxe \
		comment TcgDxeplatform \
		comment Tpm20PlatformDxe \
		comment Tpm20Acpi \
		comment TpmSmbiosDxe \
		comment Uhcd \
		comment SBDXE \
		comment Metronome \
		comment WatchdogTimer \
		comment CpuIoDxe \
		comment CRBDxe \
		comment EndlessbootDxe \
		comment TyanDxe \
		comment TyanBadDimmDxe \
		comment S7106_PROJECT_DXE \
		comment CapsuleRuntimeDxe \
		comment RuntimeDxe \
		comment PiSmmIpl \
		comment CpuCsrAccess \
		comment CrystalRidge \
		comment JedecNvDimm \
		comment PchResetRuntime \
		comment SmmControl \
		comment CryptoDXE \
		comment GenericElog \
		comment PlatformReset \
		comment SvSmmSupport \
		comment TraceHubStatusCodeHandlerRuntimeDxe \
		comment SmiFlashDxe \
		comment ConSplitter \
		comment GraphicsConsole \
		comment GenericSio \
		comment PciBus \
		comment TerminalSrc \
		comment SerialIo \
		comment CsmBlockIo \
		comment CsmVideo \
		comment IScsiDxe \
		comment PostReport \
		comment DcScreen \
		comment Nvme \
		comment PiSmmCore \
		comment FlashDriverSmm \
		comment NvramSmm \
		comment CpuIo2Smm \
		comment SmmLockBox \
		comment PcRtcSmm \
		comment PiSmmCpuDxeSmm \
		comment MemRas \
		comment CpuCsrAccessSMM \
		comment CrystalRidgeSMM \
		comment JedecNvDimmSMM \
		comment IioSmm \
		comment FpgaSmm \
		comment HwpLvtSmm \
		comment SmbusRecovery \
		comment QuiesceSupport \
		comment PchInitSmm \
		comment PchSmiDispatcher \
		comment PchSmbusSmm \
		comment HeciSmm \
		comment Heci3Smm \
		comment CryptoSMM \
		comment MicrocodeUpdate \
		comment SmmS3SaveState \
		comment SmmGenericSio \
		comment OA3_SMM \
		comment SmmPciRbIo \
		comment RuntimeMemoryHoleSmm \
		comment SmiVariable \
		comment RuntimeSmm \
		comment SmmGenericElog \
		comment KbcEmul \
		comment NvmeSmm \
		comment Ofbd \
		comment AcpiSmmPlatform \
		comment PowerButtonHandler \
		comment EmcaErrorLog \
		comment PartialMirrorHandler \
		comment MainErrorHandler \
		comment PlatformErrorHandler \
		comment ProcessorErrorHandler \
		comment AmiErrorHandlerMain \
		comment FpgaErrorHandler \
		comment PcieErrorHandler \
		comment PcieErrorEnable \
		comment McBankErrorInjection \
		comment LastBootErrorLog \
		comment SvSmmHandler \
		comment TraceHubStatusCodeHandlerSmm \
		comment RTCWakeup \
		comment SmbiosDmiEdit \
		comment SmiFlash \
		comment TcgSmm \
		comment TpmClearOnRollbackSmm \
		comment TcoSmi \
		comment AcpiModeEnable \
		comment PiSmmCommunicationSmm \
		comment TyanNvdimmAdrTrigger \
		comment FullShell \
		comment StatusCodeDxe \
		comment StatusCodeSmm \
		comment FlashDriver \
		comment NvramDxe \
		comment PcdDxe \
		comment DxeSelStatusCode \
		comment DxeFrb \
		comment CmosDxe \
		comment RegAccessDxe \
		comment RegAccessSMM \
		comment SecureBootDXE \
		comment TcgDxe \
		comment Tcg2Dxe \
		comment TcgPlatformSetupPolicy \
		comment TyanBoardDXE \
		comment AMITSE \
		comment AMITSESetupData \
		comment OPAPlatConfigSkt0 \
		comment OPAPlatConfigSkt1 \
		comment PeiCore
