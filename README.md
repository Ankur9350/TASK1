# TASK1

#!/bin/bash

# internsctl script

# Section A

# 1. Manual page
man_internsctl() {
    echo "INTENSCTL(1)                                  User Commands                                 INTENSCTL(1)"
    echo ""
    echo "NAME"
    echo "       internsctl - Custom Linux command for operations"
    echo ""
    echo "SYNOPSIS"
    echo "       internsctl [OPTION]... [COMMAND] [ARGS]..."
    echo ""
    echo "DESCRIPTION"
    echo "       internsctl is a custom Linux command that provides various operations on the system."
    echo ""
    echo "       Available commands:"
    echo "       cpu       Get CPU information"
    echo "       memory    Get memory information"
    echo "       user      User-related operations"
    echo "       file      File-related operations"
    echo ""
    echo "OPTIONS"
    echo "       --help     Show this help message"
    echo "       --version  Show command version"
    echo ""
    echo "SEE ALSO"
    echo "       More information can be found at the project repository: <repository_url>"
}

# 2. --help option
help_internsctl() {
    echo "Usage: internsctl [OPTION]... [COMMAND] [ARGS]..."
    echo ""
    echo "Options:"
    echo "  --help     Show this help message"
    echo "  --version  Show command version"
    echo ""
    echo "Commands:"
    echo "  cpu       Get CPU information"
    echo "  memory    Get memory information"
    echo "  user      User-related operations"
    echo "  file      File-related operations"
}

# 3. --version option
version_internsctl() {
    echo "internsctl v0.1.0"
}

# Section B

# Part1 | Level Easy

# Get CPU information
cpu_getinfo() {
    lscpu
}

# Get memory information
memory_getinfo() {
    free
}

# Part2 | Level Intermediate

# Create a new user
user_create() {
    if [ -z "$1" ]; then
        echo "Error: Missing username. Usage: internsctl user create <username>"
    else
        sudo useradd -m "$1"
        echo "User '$1' created successfully."
    fi
}

# List all regular users
user_list() {
    if [ "$1" = "--sudo-only" ]; then
        getent passwd | grep -E '\bsudo\b' | cut -d: -f1
    else
        getent passwd | grep -E '/home' | cut -d: -f1
    fi
}

# Part3 | Advanced Level

# Get file information
file_getinfo() {
    local file="$1"

    if [ -z "$file" ]; then
        echo "Error: Missing file name. Usage: internsctl file getinfo <file-name>"
        return 1
    fi

    if [ ! -e "$file" ]; then
        echo "Error: File '$file' not found."
        return 1
    fi

    local size=$(wc -c <"$file")
    local permissions=$(stat -c "%A" "$file")
    local owner=$(stat -c "%U" "$file")
    local last_modified=$(stat -c "%y" "$file")

    case "$2" in
        --size|-s)
            echo "$size"
            ;;
        --permissions|-p)
            echo "$permissions"
            ;;
        --owner|-o)
            echo "$owner"
            ;;
        --last-modified|-m)
            echo "$last_modified"
            ;;
        *)
            echo "File: $file"
            echo "Access: $permissions"
            echo "Size(B): $size"
            echo "Owner: $owner"
            echo "Modify: $last_modified"
            ;;
    esac
}

# Main function

case "$1" in
    man)
        man_internsctl
        ;;
    --help)
        help_internsctl
        ;;
    --version)
        version_internsctl
        ;;
    cpu)
        cpu_getinfo
        ;;
    memory)
        memory_getinfo
        ;;
    user)
        shift
        case "$1" in
            create)
                shift
                user_create "$@"
                ;;
            list)
                shift
                user_list "$@"
                ;;
            *)
                echo "Error: Invalid user command. Usage: internsctl user [create|list] <options>"
                ;;
        esac
        ;;
    file)
        shift
        case "$1" in
            getinfo)
                shift
                file_getinfo "$@"
                ;;
            *)
                echo "Error: Invalid file command. Usage: internsctl file [getinfo] <options> <file-name>"
                ;;
        esac
        ;;
    *)
        echo "Error: Invalid command. Use 'internsctl --help' for usage information."
        ;;
esac
