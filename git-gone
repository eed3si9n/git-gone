#!/bin/bash

usage() {
  cat <<EOF
usage: git gone [-pldD] [<branch>=origin]
OPTIONS
	-p	prune remote branch
	-n	dry run: list the gone branches
	-d	delete the gone branches
	-D	delete the gone branches forcefully
EXAMPLES
git gone -pn	prune and dry run
git gone -d	delete the gone branches
EOF
}

POSITIONAL=()
OPTIONS=()

while [[ $# -gt 0 ]]
do
  key="$1"

  case $key in
    -*)
    OPTION="$1"
    shift # past argument
    ;;
    *)    # unknown option
    POSITIONAL+=("$1") # save it in an array for later
    shift # past argument
    ;;
  esac
done
set -- "${POSITIONAL[@]}" # restore positional parameters

if [ $# -eq 0 ]; then
  BRANCH="origin"
elif [ $# -eq 1 ]; then
  BRANCH="$1"
else
  usage && echo "too many arguments!" && exit 1
fi

let prune=0
let list=0
let delete=0
let force=0
for (( i=1; i < ${#OPTION}; i++ )); do
  op="${OPTION:$i:1}"
  case $op in
    p)
    let prune=1
    ;;
    n)
    let list=1
    ;;
    d)
    let delete=1
    ;;
    D)
    let delete=1
    let force=1
    ;;
    *)
    usage && echo "unknown option $op!" && exit 1
    ;;
  esac
  OPTIONS+="$op"
done

if [ ${#OPTION} -eq 0 ]; then
  usage && exit 1
fi
if [ $prune -eq 1 ]; then
  git remote prune "$BRANCH"
fi

gone="$BRANCH/.*: gone]"
if [ $delete -eq 1 ]; then
  if [ $force -eq 1 ]; then
    git branch -vv | grep "$gone" | awk '{print $1}' | xargs git branch -D
  else
    git branch -vv | grep "$gone" | awk '{print $1}' | xargs git branch -d
  fi
elif [ $list -eq 1 ]; then
  git branch -vv | grep "$gone"
fi
