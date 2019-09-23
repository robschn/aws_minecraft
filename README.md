# AWS Minecraft Server

## Introduction
This guide will walk you through setting up the Minecraft server and the Discord bot for AWS. There's a lot of parts, so please read this guide entirely before starting. The bulk of code I'm using are from two very smart people, JamesMatchett and trevor-laher. Please check out their repos for more!

## Setting up AWS Minecraft Server
Import AWS guide
Add in creating an ACL so the server is not open to everyone
Add installing and initiating screens
Set up the cronjob and autoshutdown script

This step will configure the AWS Linux server to run the minecraft server. It will include SSH connecting to the server, gaining admin privileges, installing java, directory setup, moving shell scripts onto the server, and making a CRON job for these shell scripts. Note that this step will include both an SSH client and a File Transfer client (such as FileZilla) on your PC.

The first step will be to get SSH into the server instance. Using the key downloaded from AWS in the section above, add this key to PuTTY or simply access it through command line. The IP address can be obtained by entering the server password on the site, or through the EC2 Dashboard, selecting the iPV4 address from the corresponding instanceID in your configuration file. For MacOS and Linux systems

ssh -i pathToYourKeyFileHere ubuntu@IPAddress

Make the ubuntu user admin if it isn't already with:

adduser ubuntu sudo

The next step will be to install JavaJDK onto your system. For newer versions you may enter: sudo apt install openjdk-11-jdk-headless If this doesn't work you can use sudo apt list and search through these packages for an alternative java version.

Open up an FTP client such as FileZilla and connect to the same address as the same user with the same IP address. Drag all files from the instanceSetup folder from this repository, into the root directory of the current user (probably ubuntu, for the purposes of these commands I will be using ubuntu, but feel free to replace with your own user if appropriate).

Download the desired Minecraft server version from https://www.minecraft.net/en-us/download/server/, rename it server.jar and drag it into the root directory of the user using FileZilla.

Using the FTP client, create a new folder in the root directory of the current user called screens
OR
In the SSH client, create a folder in the current directory with the command: sudo mkdir screens

Then execute the following command: sudo chmod 700 /home/ubuntu

Then execute the next command: export SCREENDIR=/home/ubuntu/screens

Then execute the command: sudo crontab /home/ubuntu/crontab -u ubuntu

Feel free to close the server through the AWS console or execute the command: sudo /sbin/shutdown -P +1

At this point you may restart the server from the Web Application using the password you configured. You should then be able to play!

## Setting AWS Discord Bot
Import Discord Bot guide
Add in restricting bot and users to a specified group

Download node and aws-cli onto your machine https://nodejs.org/en/ https://aws.amazon.com/cli/
Run aws configure from command prompt on the machine you wish to host the bot from
Create a discord application here https://discordapp.com/developers/applications/ and create a bot user under it
Add the bot account to your discord server
Create a dedicated role on your discord server as well as a dedicated text channel for the bot
Gather the required credentials and IDs needed from AWS and Discord mentioned below
In the DiscordBot folder make a Json file called "config.json" and add the following values in this format

{
"BOT_TOKEN": "*Insert your discord bot token here*",
"ROLEID": "*Insert the Discord role ID of a role that only people you want using the bot have*",
"CHANNELID": "*Insert the Discord channel ID of the text channel where you want the bot to be controlled from*",
"ACCESSKEY": "*Insert the AWS generated access key here*",
"SECRETKEY": "*Insert the AWS generated secret key here*",
"SESSIONTOKEN": "*Insert the AWS session token here (may not be needed)*",
"INSTANCE": "*Insert the instance ID of the AWS instance you wish to toggle from the bot*"
"MESSAGELOGGING": "F" (or leave as 'T' if you want specific error messages logged to the Discord channel which may contain sensitive info like keys or instance IDs)
}
Command prompt into the DiscordBot folder and run

node src/Bot.js
Commands:

$aws start (Starts the specified instance)
$aws stop (Stops the specified instance)
$aws status (Returns information about the instance, i.e. if it is running, it's IP, it's last startTime)
You, and anyone else you give the role and channel access to, should be able to toggle the selected AWS instance on or off when needed as well as view instance information on demand.
