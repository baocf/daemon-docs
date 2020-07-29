#!/bin/sh

REPOS="$1"
REV="$2"



#count commited file num

check=$(svnlook changed -t $REV $REPOS | awk 'NF!=2{for(i=2;i<=NF;i++)if(i!=NF)printf $i" ";if(i=NF)printf $i","}')

#compare files

if [ "$check" != "" ]
then
         echo "***************************************" 1>&2
                 echo "The file name contains spaces.:" 1>&2
         echo "${check}" 1>&2
                 echo "please fix it and try again." 1>&2
         echo "***************************************" 1>&2
         exit 1
fi



LOGMSG=`svnlook log -t "$REV" "$REPOS" | grep "[a-zA-Z0-9]" | wc -c`
if [ "$LOGMSG" -lt 5 ];
then
  echo "***************************************" 1>&2
  echo -e "at least 5 words!" 1>&2
  echo "please fix it and try again." 1>&2
  echo "***************************************" 1>&2
  exit 1
fi

exit 0