#!/bin/bash

# Check if ADB is installed
if ! command -v adb &> /dev/null
then
    echo "ADB is not installed. Please install ADB and try again."
    exit
fi

# Get list of connected devices
devices=($(adb devices | grep -v List | cut -f1))

# Check if any devices are connected
if [ ${#devices[@]} -eq 0 ]; then
    echo "No devices are connected."
    exit
fi

# Display list of connected devices
echo "Connected devices:"
for i in "${!devices[@]}"; do
    echo "$i: ${devices[$i]}"
done

# Prompt user to select device
echo "Please select a device to open with scrcpy:"
read device_index

# Check if user input is valid
if ! [[ "$device_index" =~ ^[0-9]+$ ]] || [ $device_index -lt 0 ] || [ $device_index -ge ${#devices[@]} ]; then
    echo "Invalid input. Please enter a number between 0 and $((${#devices[@]}-1))."
    exit
fi

# Open device with scrcpy
device=${devices[$device_index]}
echo "Opening $device with scrcpy..."
scrcpy -s $device