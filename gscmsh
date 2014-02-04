#!/bin/bash

#--------------------------Function Definitions--------------------------------#

#checks if $1 is the GetSimple CMS top level directory with the plugins and theme folders in it 
isWebsiteRootDirectory() {
  if [ -n "$(ls $1 | grep plugins)" ] && [ -n "$(ls $1 | grep theme)" ]; then  
    return 0
  else 
    return 1
  fi
}

#iteratively check directories up to the root direcotry of the site
#if used withe the --check option, it does not write to stdout, but 
#only determines, if the script has been called from within a valid directory
getWebsiteRootDirectory() {
  #dir is the directory to start in
  dir="./" 

  while ! isWebsiteRootDirectory $dir; do
    dir=$dir"../"
    #if root of filesystem has been reached exit with error status
    if [ "$(realpath $dir)" == "/" ]; then
      exit 1
    fi
  done
  
  if [ "$1" != "--check" ]; then
    echo "$dir"
  fi 
  
  exit 0
}

installPlugin() {
  zipfile=$(basename "$1")
  
  #switch to the plugin directory
  pushd $(getWebsiteRootDirectory)"plugins"
  
  #download and install the plugin
  wget $1
  
  #TODO: Make sure that plugin zips are, in contrast to themes, consistently structured (compare line 59)
  unzip $zipfile
  rm $zipfile  
  
  #return to former directory
  popd
}

installTheme() {
  zipfile=$(basename "$1")
  
  #switch to the theme directory
  pushd $(getWebsiteRootDirectory)"theme"
  
  wget $1
  #some zips can be directly unzipped, others must be extracted into an extra directory. User intervention necessary.
  #TODO: Automate the detection of toplevelfolder within the archive and only ask for name if necessary
  unzip -l $zipfile  
  echo "If the content of the zip is organised under one subdirectory press Enter. Otherwise enter a name for the theme: "
  read input
  
  if [ -z "$input" ]; then
    #input empty, assuming, that the zipfile has to be extracted only
    unzip $zipfile
  else
    #create directory with name entered
    mkdir $input
    unzip $zipfile -d $input
  fi
  
  rm $zipfile
  
  #return to former directory
  popd    
}

#TODO: Make the output nicer
#no duplicates (e.g. only the dirs, not the .php files)
listPlugins() {
  ls $(getWebsiteRootDirectory)"plugins/"
}

listThemes() {
  ls $(getWebsiteRootDirectory)"theme"
}


#---------------------This is where the actualy execution begins-----------------------#

#check if script has been called from within a Get Simple CMS installation directory
if ! $(getWebsiteRootDirectory --check); then
  echo "This script must be called from within a proper installation directory of GetSimple CMS"
  exit 1
fi

case "$1" in
  "pinstall")  installPlugin $2
  ;;
  "tinstall") installTheme $2
  ;;
  "plist") listPlugins
  ;;
  "tlist") listThemes
  ;;
  *) echo "Usage: $(basename $0) <action> [<parameters>]
  Possible actions are:
    pinstall <URL> - Install a Plugin
    tinstall <URL> - Install a Theme
    plist - List Plugins installed
    tlist - List Themes installed
    
  Where <URL> stands for a URL pointing to the zip of the plugin or script as can be found on http://get-simple.info/extend/"
  ;;
esac
