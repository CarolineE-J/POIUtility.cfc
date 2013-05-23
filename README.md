
# Railo fix for clashing jar files.

By Caroline Espley-Jones

The POIUtility.cfc is a ColdFusion component that helps you read Microsoft Excel files into ColdFusion 
queries as well as convert ColdFusion queries into multi-sheet Microsoft Excel files.

## Issue

Since the Railo upgrade, I noticed that POIUtility.cfc was not working the way it was originally. After much digging around and google-ing, I found it was due to the fact that, in Railo, there are two jar files with the same class.

Jar 1: apache-poi.jar (The new jar file, recieved after the update).
Jar 2: apache-poi-tm-extractors.jar (The old jar file, which remained there after the update).

The class that was being called was "HSSFWorkbook" (around line 881 in the CFC).

Since the createObject() method searches for the class first (child-first), not caring which jar file it is located in, the CFC was finding the old class instead of the shiney new one and was throwing up all kinds of errors.


## Solution

Originally, I thought deleting the old jar file would solve everything, and it turns out it did but that was not a permanent solution. For one, to make these changes on the servers I use, it would require digging into the *live* server and manually deleting jar files from the upgrade. Plus, when I upgrade Railo the next time, this file would most likely be included again, anyway.

Javaloader was suggested by quite a few people as you can define specifically where you want a jar file to be found. The only issue is the location of the jar files differ for each installation and OS. For example, I am on Ubuntu and my Railo jar files are located in /opt/railo/lib/ whereas a Window user's was located in C:/railo/lib.

Using {railo-server}, I could locate the installation files and back track to where I wanted the Javaloader to look for the files. This works accross all OS I could test - Mac, Ubuntu and Windows.

This solved the whole issue I was having. I could still use Ben Nadel's code and only change a few lines to make it work with the Railo upgrade.