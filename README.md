# video_notes
notes on video


******* HOW media SDK app is calling right va-api **********


MSDK(intel media SDK） app, usually linked statically against libmfx.a(mfx_dispatcher: under /opt/intel/media/opensource), 
mfxvideo++.h is a thin header-only cpp wrapper to libmfx.a. 

[App] <==MFXxxx API==> [libmfx.a] <== search so files ==> [libmfxhw64-p.so] <==> [libva.so.1]

   libmfx.a will assume media SDK's userspace part has been installed and search under following hard-coded location:
          /opt/intel/mediasdk/lib64(if mfx_dispatcher is configured as cmake -D__ARCH=intel64) 
      or  /opt/intel/mediasdk/lib32(otherwise)
      and it seaches for filename patter satisfy basename.<mj>.<mn> where:
         [*]  basename is libmfxhw64-p.so in HW/linux64 cases
         [*]  mj & mn is numbers
      so it will omit the symbolic link(cause it has  no version), after choose one core MediaSDK libraries, it will 
      dynamically load it and also the dependent libva.so.1 files.

[libva.so.1]

actuall driver used by libva is configured through following enviroment variables which will be 
setup by mediaSDK install script:

  $ cat /etc/profile.d/intel-mediasdk.sh 
  # add libva driver path/name exporting for intel media solution
  export LIBVA_DRIVERS_PATH=/opt/intel/mediasdk/lib64
  export LIBVA_DRIVER_NAME=iHD
  
unlike the open-sourced i965/i915 VA-ＡＰＩ-driver,this one is the closed-sourced ＶＡ-API-driver for intel HD graphic provided by 
media ＳＤＫ.

