#!/usr/bin/env bash
#
# Copyright (C) 2022 TheHitMan7
# Android Kernel Build Script
#

# Set Initials
PARENT_DIR=$(pwd)
KERNEL_DIR="kernel"
CODENAME="ginkgo"
SOURCE=$PARENT_DIR/$KERNEL_DIR/$CODENAME
OUT=$PARENT_DIR/$KERNEL_DIR/out

# Create Initials
mkdir -p $PARENT_DIR/$KERNEL_DIR/$CODENAME 2>/dev/null;

# Check Toolchain Source
[ ! -d "$PARENT_DIR/$KERNEL_DIR/clang" ] && CLANG="true" || CLANG="false"
[ ! -d "$PARENT_DIR/$KERNEL_DIR/aarch64-maestro-linux-android" ] && GCC64="true" || GCC64="false"
[ ! -d "$PARENT_DIR/$KERNEL_DIR/arm-maestro-linux-gnueabi" ] && GCC32="true" || GCC32="false"

# Clone Toolchain Source
if [ "$CLANG" == "true" ]; then
  git clone https://github.com/TheHitMan7/clang.git -b master $PARENT_DIR/$KERNEL_DIR/clang 2>/dev/null
fi
if [ "$GCC64" == "true" ]; then
  git clone https://github.com/TheHitMan7/aarch64-maestro-linux-android.git -b master $PARENT_DIR/$KERNEL_DIR/aarch64-maestro-linux-android 2>/dev/null
fi
if [ "$GCC32" == "true" ]; then
  git clone https://github.com/TheHitMan7/arm-maestro-linux-gnueabi.git -b master $PARENT_DIR/$KERNEL_DIR/arm-maestro-linux-gnueabi 2>/dev/null
fi

# Check Kernel Source
[ -z "$(ls -A $PARENT_DIR/$KERNEL_DIR/$CODENAME)" ] && KTREE="true" || KTREE="false"

# Clone Kernel Source
if [ "$KTREE" == "true" ]; then
  git clone https://github.com/TheHitMan7/android_kernel_sm6125.git -b linux-4.14.y $SOURCE 2>/dev/null;
fi

# Check AnyKernel Source
[ ! -d "$PARENT_DIR/$KERNEL_DIR/AnyKernel3" ] && ANYKERNEL="true" || ANYKERNEL="false"

# Clone AnyKernel Source
if [ "$ANYKERNEL" == "true" ]; then
  git clone https://github.com/TheHitMan7/AnyKernel3.git -b master $PARENT_DIR/$KERNEL_DIR/AnyKernel3 2>/dev/null;
fi

# Set Toolchain Source
export CC="$PARENT_DIR/$KERNEL_DIR/clang/bin/clang"
export CLANG_TRIPLE="$PARENT_DIR/$KERNEL_DIR/aarch64-maestro-linux-android/bin/aarch64-maestro-linux-gnu-"
export CROSS_COMPILE="$PARENT_DIR/$KERNEL_DIR/aarch64-maestro-linux-android/bin/aarch64-maestro-linux-gnu-"
export CROSS_COMPILE_ARM32="$PARENT_DIR/$KERNEL_DIR/arm-maestro-linux-gnueabi/bin/arm-maestro-linux-gnueabi-"

# Build Android Kernel
if [ "$1" == "ck" ]; then
  make clean
  make mrproper
fi
if [ "$1" == "mk" ]; then
  cd $SOURCE
  export ARCH=arm64
  export SUBARCH=arm64
  export LOCALVERSION=
  export KBUILD_BUILD_USER=TheHitMan
  export KBUILD_BUILD_HOST=ILLYRIA
  export KBUILD_COMPILER_STRING="$(${CC} --version | head -n 1 | perl -pe 's/\(http.*?\)//gs' | sed -e 's/  */ /g')"
  make O=$OUT ARCH=arm64 vendor/ginkgo-perf_defconfig
  make O=$OUT ARCH=arm64 CC="$CC" CLANG_TRIPLE="$CLANG_TRIPLE" CROSS_COMPILE="$CROSS_COMPILE" CROSS_COMPILE_ARM32="$CROSS_COMPILE_ARM32" -j$(nproc --all)
fi

# Create AnyKernel Package
if [ -f "out/arch/arm64/boot/Image.gz-dtb" ]; then
  cd $PARENT_DIR/$KERNEL_DIR
  KERN_IMG="out/arch/arm64/boot/Image.gz-dtb"
  DATE=`date +"%Y%m%d"`
  TOKEN=$(( $RANDOM % 100 ))
  cp -f $KERN_IMG $PARENT_DIR/$KERNEL_DIR/AnyKernel3/Image.gz-dtb
  cd $PARENT_DIR/$KERNEL_DIR/AnyKernel3
  zip -r9 RIGEL-X.zip *
  mv $PARENT_DIR/$KERNEL_DIR/AnyKernel3/RIGEL-X.zip $PARENT_DIR/$KERNEL_DIR/RIGEL-X-$DATE-${TOKEN}.zip
  cd ../..
  rm -rf $PARENT_DIR/$KERNEL_DIR/AnyKernel3
  START=$(date +"%s"); END=$(date +"%s"); DIFF=$((END - START))
  echo -e "Build took $((DIFF / 60)) minute(s) and $((DIFF % 60)) seconds."
fi
