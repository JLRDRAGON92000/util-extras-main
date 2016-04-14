#!/bin/bash

# jrun: Compile and run a Java program
# Requires Java
# Compatible with ainfo PS1 mode (Also in Util/)

LF='\012';

if [ "$(basename $0)" != "jrun" ];
then
	echo -e "\e[01;31mNOTICE: \e[00mThis copy of jrun was installed under a name other than \"jrun\".";
fi

compile () {
	echo -ne "\e[01;34mCompiling class $(basename $filename)... \e[00m";
	pwdbak=$PWD;
	if echo "$2" | grep -q "/";
	then
		clsp="$PWD/$2";
		dircount=$(echo -e "$(echo $clsp | tr \"/\" \"\\\n\")" | wc -l);
		# echo -e "$(echo $clsp | tr \"/\" \"\\\n\")";
		# echo $dircount;
		let "dircount-=1"
		clspath=$(echo $clsp | cut -f1-$dircount -d"/");
		# echo -e $clspath;
		cd $clspath;
	fi
	# echo $PWD;
	javac "$(basename $filename).java" &>./.jc.tmp;
	stattemp="$?";
	if [ "$stattemp" -ne "0" ];
	then
		echo "$(date "+%d %b %Y, %H:%M")|$(basename $filename)|FAILURE" >"./.javaproj.lock";
	else
		echo "$(date "+%d %b %Y, %H:%M")|$(basename $filename)|SUCCESS" >"./.javaproj.lock";
	fi;
	if [ "$stattemp" -eq "1" ];
	then
		echo "";
		echo -e "\e[01;31m$(cat ./.jc.tmp)\e[00m";
		rm "./.jc.tmp";
		exit 1;
	elif [ "$stattemp" -eq "2" ];
	then
		echo -e "\n\e[01;31mClass $(basename $filename) not found";
		rm "./.jc.tmp";
		exit 2;
	elif [ "$stattemp" -eq "130" ];
	then
		echo -e "\n\e[01;31mInterrupted\e[00m";
		rm "./.jc.tmp";
		exit 130;
	else
		echo -e "\e[01;32mDone.\e[00m";
		rm "./.jc.tmp";
	fi
}

runclass () {
	if [ -z $pwdbak ];
	then
		echo -n "";
	else
		cd $pwdbak;
	fi
	cls="";
	clspath="";
	if echo "$2" | grep -q "/";
	then
		cls="$(basename $2)";
		clsp="$PWD/$2";
		dircount=$(echo -e "$(echo $clsp | tr \"/\" \"\\\n\")" | grep -c "
");
		# echo -e "$(echo $clsp | tr \"/\" \"\\\n\")";
		# echo $dircount;
		let "dircount-=1"
		clspath=$(echo $clsp | cut -f1-$dircount -d"/");
		# echo -e $clspath;
		
		clsl="-cp $clspath/ $(basename $2)"
	else
		cls="$(basename $2)";
		clspath="$PWD";
		clsl="$(basename $2)";
	fi
	
	# echo $cls;
	# echo $clsl;
	
	echo -e "\e[01;34mRunning class $cls\e[00m";
	echo "";
	
	# echo $@;
	aindex=1;
	aargs="";
	for targ in "$@";
	do
		# echo -n "$targ: ";
		if [ $aindex -gt 2 ];
		then
			aargs="$aargs $targ";
			# echo "Added";
		# else
			# echo "Not added";
		fi
		let "aindex+=1";
	done
	# echo $aargs;
	
	# echo $PWD;
	# echo $clspath;
	cd $clspath;
	
	# echo "java $clsl $aargs"
	java $clsl $aargs;
	statt="$?";
	echo "";
	if [ "$statt" -eq 130 ];
	then
		echo -e "\e[01;31mClass $cls Ctrl-C'd\e[00m";
		exit 130;
	elif [ "$statt" -ne 0 ];
	then
		echo -e "\e[01;31mClass $cls failed with status code $statt.\e[00m";
		exit $statt;
	else
		echo -e "\e[01;32mClass $cls exited with status code $statt.\e[00m";
	fi
	exit 0;
}

param="$1";
filename="$2";

if [ "$param" == "-a" ];
then
	compile $@;
	runclass $@;
elif [ "$param" == "-r" ];
then
	if [ ! -e "$PWD/$filename.class" ];
	then
		echo -e "\n\e[01;31mClass $(basename $filename) not found";
		exit 2;
	else
		runclass $@;
	fi
elif [ "$param" == "-c" ];
then
	compile $@;
elif [ "$param" == "-x" ];
then
	# Attempt to remove old class files, and fail gracefully and carry on if they do not exist
	# Also, print out an information line if they do not exist
	rm "./$2.class" 2>/dev/null;
	rmclsstmp="$?";
	rm ./$2\$*.class 2>/dev/null;
	rmassocstmp="$?";
	
	if [ "$rmclsstmp" -eq 0 ]; then echo -n "";
	elif [ "$rmassocstmp" -eq 0 ]; then echo -n "";
	else
		echo -e "\e[01;33mClass $(basename $2) has no pre-existing compiled classes\e[00m";
	fi
	
	compile $@;
	runclass $@;
elif [ "$param" == "-us" ];
then
	echo "$(date "+%d %b %Y, %H:%M")|$(basename $filename)|SUCCESS" >"./.javaproj.lock";
	echo -e "\e[01;34mFalsified .javaproj.lock: $(basename $filename) \"built\" at $(date "+%d %b %Y, %H:%M") with successful result";
elif [ "$param" == "-uf" ];
then
	echo "$(date "+%d %b %Y, %H:%M")|$(basename $filename)|FAILURE" >"./.javaproj.lock";
	echo -e "\e[01;34mFalsified .javaproj.lock: $(basename $filename) \"built\" at $(date "+%d %b %Y, %H:%M") with failed result";
elif [ "$param" == "-i" ];
then
	cp "$0" "$HOME/bin/jrun";
	chmod 755 "$HOME/bin/jrun";
	echo "jrun successfully installed at $HOME/bin/jrun.";
elif [ "$param" == "-if" ];
then
	cp "$0" "$2";
	chmod 755 "$2";
	echo "jrun successfully installed at $2.";
elif [ "$param" == "--help" ];
then
	echo -e "\e[01;37mjrun\e[00m - Compiles and runs Java classes\e[00m";
	echo -e "usage: \e[01;37m$(basename $0)\e[00m -a|-c|-r|-x|-i|-if classname [args]";
	echo -e "options:";
	echo -e "	\e[01;37m-a\e[00m	Compile and run the given class";
	echo -e "	\e[01;37m-c\e[00m	Compile the given class";
	echo -e "	\e[01;37m-r\e[00m	Run the given class";
	echo -e "	\e[01;37m-x\e[00m	Compile and run the given class, but delete preexisting class files first";
	echo -e "	\e[01;37m-i\e[00m	Install jrun to your system at the default location ~/bin/jrun";
	echo -e "	\e[01;37m-if\e[00m	Install jrun to your system at given path";
	# This isn't the C9-specific implementation anymore, so this line isn't needed
	# echo -e "\e[01;37mNOTE: \e[00mThis implementation of jrun is designed for use on Cloud9. It may need to be adapted for use on your particular setup.";
else
	echo -e "\e[01;31m$(basename $0): invalid option -- '${param:1}'\e[00m";
	echo -e "\e[01;31m$(basename $0): Try \`$(basename $0) --help' for more information.\e[00m"
	exit 2;
fi
