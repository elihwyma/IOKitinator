#!/bin/sh

nosudo() {
    echo "This cannot be ran as root or with sudo."
    exit 1
}

crd=$PWD

[[ $UID == 0 || $EUID == 0 ]] && nosudo
echo "==================================================================="
echo "I am not responsible if anything goes wrong, this is provided as is"
echo "==================================================================="

sudo -p "Password for installation: " printf "" || exit 1

addToXcode() {
  defaultXcodePath="/Applications/Xcode.app/"
  read -p "Path to Xcode, leave blank for default (/Applications/Xcode.app/)" xcodePath
  xcodePath="${xcodePath:-$defaultXcodePath}"
  if [ "%xcodePath:~-1%" != "/" ]; then xcodePath="${xcodePath}/"
  fi

  headersPath="${xcodePath}Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/Frameworks/IOKit.framework/Versions/A/Headers"
  destinationPath="${xcodePath}Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/System/Library/Frameworks/IOKit.framework"

  sudo cp -r $headersPath $destinationPath
  if [[ $? -ne 0 ]]; then
    echo "Failed to copy headers, check the path!"
    exit 1;
  fi
  echo "Successfully copied the headers to Xcode SDK, about to try and grab OSTypes.h"
  sudo wget -P ${xcodePath}Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/usr/include/libkern -q https://opensource.apple.com/source/xnu/xnu-4570.41.2/libkern/libkern/OSTypes.h
  if [[ $? -ne 0 ]]; then
    echo "Failed to get OSTypes.h, is the connection good?"
    exit 1;
  fi
  echo "Successfully added OSTypes.h to Xcode SDK, you can now add IOKit to your projects!"

  defaultPotentialTheos="Y"
  read -p "Do you want to also add to Theos SDK? (Default: Y)" potentialTheos
  potentialTheos="${potentialTheos:-$defaultPotentialTheos}"
  if [ $potentialTheos == "N" ] ; then
    exit 1;
  elif [ $potentialTheos == "Y" ] ; then
    addToTheos
  else
    echo "Invalid input"
  fi


}

addToTheos() {
  read -p "Path to Theos iOS SDK (Example: ~/theos/sdks/iPhoneOS13.3.sdk/)" theosSDKPath
  if [ "%theosSDKPath:~-1%" != "/" ]; then theosSDKPath="${theosSDKPath}/"
  fi

  defaultXcodePath="/Applications/Xcode.app/"
  read -p "I need your Xcode directory so I can grab the headers, leave blank for default (/Applications/Xcode.app/)" xcodePath
  xcodePath="${xcodePath:-$defaultXcodePath}"
  if [ "%xcodePath:~-1%" != "/" ]; then xcodePath="${xcodePath}/"
  fi
  headersPath="${xcodePath}Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/Frameworks/IOKit.framework/Versions/A/Headers"
  destinationPath="${theosSDKPath}System/Library/Frameworks/IOKit.framework"

  sudo cp -r $headersPath $destinationPath
  if [[ $? -ne 0 ]]; then
    echo "Failed to copy headers, check the path!"
    exit 1;
  fi
  echo "Successfully copied the headers to Theos SDK, about to try and grab OSTypes.h"
  sudo wget -P ${theosSDKPath}usr/include/libkern -q https://opensource.apple.com/source/xnu/xnu-4570.41.2/libkern/libkern/OSTypes.h
  if [[ $? -ne 0 ]]; then
    echo "Failed to get OSTypes.h, is the connection good?"
    exit 1;
  fi
  echo "Successfully added OSTypes.h to Theos SDK, you can now add IOKit to your projects!"
}

main() {
  defaultPotentialTheos="Y"
  read -p "Do you want to add to Theos SDK? (Default: Y)" potentialTheos
  potentialTheos="${potentialTheos:-$defaultPotentialTheos}"
  if [ $potentialTheos == "Y" ] ; then
    addToTheos
  else
    echo "Ignoring Theos"
  fi

  defaultPotentialXcode="Y"
  read -p "Do you want to add to Xcode iOS SDK? (Default: Y)" defaultPotentialXcode
  potentialXcode="${potentialXcode:-$defaultPotentialXcode}"
  if [ $potentialXcode == "Y" ] ; then
    addToXcode
  else
    echo "Ignoring Xcode"
  fi
}

main
