---
title: Tag Object As Plant
description: Demonstrates how to tag an object as a Flamingo nXt plant in RhinoScript.
author: dale@mcneel.com
apis: ['RhinoScript']
languages: ['VBScript']
platforms: ['Windows']
categories: ['Flamingo']
origin: http://wiki.mcneel.com/flamingo/flamingosdk/tagobjectasplant
order: 1
keywords: ['rhinoscript', 'vbscript', 'flamingo']
layout: code-sample-rhinoscript
---

```vbnet
Option Explicit

Call Main()

Sub Main()
	Dim objPlugIn, plantFile, strObject, topiary, location, plant
	On Error Resume Next
	Set objPlugIn = Rhino.GetPluginObject("8008880f-8d13-4b2d-92b0-727e12878a4c")
	If Err Then
		MsgBox Err.Description
		Exit Sub
	End If
  plantFile = objPlugIn.GetPlantFileName("")
  If Not IsNull(plantFile) Then
	  strObject = Rhino.GetObject("Select object to tag as plant")
    If Not IsNull(strObject) And Not strObject = "" Then
      topiary = False
      location = Array(0.0, 0.0, 0.0)
      If objPlugIn.PlantFileNameIsGroundcover(plantFile) Then
        topiary = Rhino.GetBoolean("Plant topiary", Array( "Topiary", "Off", "On"), Array(topiary))
      Else
        location = Rhino.GetPoint("Plant location")
      End If
      If Not IsNull(location) And Not IsNull(topiary) Then
        Set plant = objPlugIn.NewPlant()
        plant.FileName = plantFile
        plant.Location = location
        plant.Topiary = topiary
        If objPlugIn.TagObjectAsPlant(strObject, Plant) Then
            Rhino.Print("Plant " & plantName & " assigned to selected object")
        End If
        Set plant = Nothing
      End If
    End If
	End If
	Set objPlugIn = Nothing
End Sub
```
