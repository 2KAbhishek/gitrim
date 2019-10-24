#!/bin/bash
# Trim unncecessary files from git repository
# Credits to various people over at stackoverflow

CWD=$PWD

# Functions

# Changes to repo provided by user
function sel_repo
{
    echo -en " \u001b[32;1m\u001b[4m Enter full path to repository :\u001b[0m "
    read -r repo_dir
    cd "$repo_dir" || return
    echo "$PWD"
}

# Makes sure all branches are up-to-date
function update_branch
{
    for branch in $(git branch -a | grep remotes | grep -v HEAD | grep -v master); do
        git branch --track "${branch##*/}" "$branch"
    done
}

# Requires dust or du for functionality
function list_largest
{
    echo -en " \u001b[32;1m\u001b[4mEnter directory depth :\u001b[0m "
    read -r depth
    if hash dust 2>/dev/null; then
        dust -d depth
    else
        du -h --max-depth="$depth" path/to/directory
    fi
}

# Trims file from git history
function trim_file
{
    echo -en " \u001b[32;1m\u001b[4mEnter file path to trim :\u001b[0m "
    read -r filepath
    git filter-branch --tag-name-filter cat --index-filter \
    "git rm -r --cached --ignore-unmatch $filepath" --prune-empty -f -- --all
}

# Remove trimmed files from local repo.
function clean_files
{
    rm -rf .git/refs/original/
    git reflog expire --expire=now --all
    git gc --prune=now
    git gc --aggressive --prune=now
}

# Push changed repo to remote
function push_change
{
    git push origin --force --all
    git push origin --force --tag
    echo -e "\u001b[31;1m Warning! Must use rebase or fresh clone on other local repos. \u001b[0m"
}

# Clean up and exit
function bye
{
    cd "$CWD" || return
    clear
    exit
}

# Infinite loop for menu
while true

do

    clear

    # Draw UI, ANSI Escape sequences used. Edit carefully!
    echo
    echo -e " \e[7m Gitrim - Trim Git Repository \e[0m"
    echo -e " \u001b[34;1m (1) Select Repository          (2) Update Branches \u001b[0m"
    echo -e " \u001b[34;1m (3) List Largest Files         (4) Trim Files \u001b[0m"
    echo -e " \u001b[34;1m (5) Clean Local Repo           (6) Push Changes  \u001b[0m"
    echo -e " \u001b[31;1m (0) Quit \u001b[0m"
    echo
    echo -e "\e[1m For best results use the options sequentially per repository.\e[0m"
    echo -en " \u001b[32;1m\u001b[4mSelect Option :\u001b[0m "

    # Save entered number
    read -r choice

    # Run appropriate choice
    case "$choice" in

        1 )
            sel_repo
            echo
            echo -e " \e[42m Changed repository. \e[0m"
            # wait for input, e.g. by pressing ENTER:
            read -r
            ;;
        2 )
            update_branch
            echo
            echo -e " \e[42m All branches up-to-date. \e[0m"
            read -r
            ;;
        3 )
            list_largest
            echo
            echo -e " \e[42m Largest files listed. \e[0m"
            read -r
            ;;
        4 )
            trim_file
            echo
            echo -e " \e[42m Selected files trimmed. \e[0m"
            read -r
            ;;
        5 )
            clean_files
            echo
            echo -e " \e[42m Trimmed files cleaned from local repository. \e[0m"
            read -r
            ;;
        6 )
            push_change
            echo
            echo -e " \e[42m Changes pushed to remote. \e[0m"
            read -r
            ;;
        0 )
            bye
            ;;
        * )
            echo -e " \e[41m Wrong option \e[0m"
            echo -e "  Please try again...  "
            sleep 1
            ;;

    esac

done

