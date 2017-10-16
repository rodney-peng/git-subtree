#!/bin/sh -e
# Usage:
#
#        prefix= [branch=] [annotate=] template split [args]
#        prefix= [message=]            template merge [args] <commit>
#
#        prefix= remote= [branch=]            template add  [args]
#        prefix= remote= [branch=] [message=] template pull [args]
#        prefix= remote= [branch=]            template push [args]
#
#        prefix= branch=- template add [args] <commit>
#
# To debug, set shell_cmd=echo to print the resulting command:
#
#        shell_cmd=echo template <command> [args]
#

# the <name> is used to identify the subtree branch and the commit among subtrees from the same repository
# it must be identical to the name used at remote
subtree_name=${name:-'name'}

# the subtree_prefixes may diff for the remote and the local
subtree_prefix=${prefix:-'path/to/subtree'}

# remote repository for the subtree
subtree_remote=${remote:-'remote'}

# branch for the subtree, the default branch name is prefixed with 'subtree-'
subtree_branch=${branch:-"subtree-$subtree_name"}
# use branch=- to disable default split branch or remote ref
subtree_branch=${subtree_branch#-}

# commit message prefix for split, make it identical for each split
subtree_annotate=${annotate:-"$subtree_name: "}
# use annotate=- to disable default split annotation
subtree_annotate=${subtree_annotate#-}

subtree_cmd="$1"
[ -z "$subtree_cmd" ] && exit
shift

if [ "$subtree_cmd" = "split" -a -n "$subtree_branch" ]; then
  split_branch="-b '$subtree_branch'"
else
  split_branch=
fi

if [ "$subtree_cmd" = "split" -a -n "$subtree_annotate" ]; then
  split_annotate="--annotate='$subtree_annotate'"
else
  split_annotate=
fi

if [ "$subtree_cmd" = "add" -o "$subtree_cmd" = "pull" -o "$subtree_cmd" = "push" ] && \
   [ -n "$subtree_remote" -a -n "$subtree_branch" ]; then
  remote_ref="$subtree_remote $subtree_branch"
else
  remote_ref=
fi

if [ "$subtree_cmd" = "pull" ]; then
  merge_msg=${message:-"subtree pull: $subtree_prefix"}
fi

if [ "$subtree_cmd" = "merge" ]; then
  merge_msg=${message:-"subtree merge: $subtree_prefix"}
fi

# use message=- to disable default merge message
merge_msg=${merge_msg#-}
merge_msg=${merge_msg:+"-m '$merge_msg'"}

# $subtree_prefix holds the prefix until here
[ -z "$subtree_prefix" ] && exit
subtree_prefix="-P '$subtree_prefix'"

# won't work without eval if the subtree_prefix has '-', e.g. 'linux-3.18.x'
${shell_cmd:-eval} git subtree $subtree_cmd $subtree_prefix $split_branch $split_annotate $merge_msg $@ $remote_ref
