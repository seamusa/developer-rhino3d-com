---
title: Picking Text Dots
description: This brief guide demonstrates how to interactively select Text Dot objects with C/C++ CRhinoGetObject.
author: dale@mcneel.com
apis: ['C/C++']
languages: ['C/C++']
platforms: ['Windows']
categories: ['Fundamentals']
origin: http://wiki.mcneel.com/developer/sdksamples/gettextdot
order: 1
keywords: ['rhino', 'selecting', 'text', 'dots']
layout: toc-guide-page
---

# Picking Text Dots

{{ page.description }}

## Problem

Imagine you would like to allow the user to interactively select text dot objects from my plugin command.  The first place you might look is the `CRhinoGetObject` class, for a text dot geometry filter.  This does not exist.  So, how to go about this?

## Solution

If `CRhinoGetObject` does not have filters to select what you want, then simply derive a new class from `CRhinoGetObject` and override the `CustomGeometryFilter` virtual function.  From within this function, simply return true if the object meets your selection criteria.

## Sample

The following example code demonstrates how to derive a new class from `CRhinoGetObject` and override the `CustomGeometryFilter` virtual function in order to allow for selection of just text dot objects.  That is, those objects whose geometry member is `ON_TextDot`...

```cpp
class CGetTextDotObject : public CRhinoGetObject
{
  bool CustomGeometryFilter(
const CRhinoObject* object,
const ON_Geometry* geometry,
ON_COMPONENT_INDEX component_index
) const;
};

bool CGetTextDotObject::CustomGeometryFilter(
    const CRhinoObject* object,
    const ON_Geometry* geometry,
    ON_COMPONENT_INDEX component_index
    ) const
{
  bool rc = false;
  if( geometry )
  {
    const ON_TextDot* p = ON_TextDot::Cast( geometry );
    if( p )
      rc = true;
  }
  return rc;
}
```

The following example code demonstrates how you can use the new class...

```cpp
CRhinoCommand::result CCommandTest::RunCommand( const CRhinoCommandContext& context )
{
  CGetTextDotObject go;
  go.SetCommandPrompt( L"Select text dots" );
  go.GetObjects( 1, 0 );
  if( go.CommandResult() != success )
    return go.CommandResult();

  const int object_count = go.ObjectCount();
  int i;
  for( i = 0; i < object_count; i++ )
  {
    const ON_TextDot* p = ON_TextDot::Cast( go.Object(i).Geometry() );
    if( p )
    {
      ON_wString sPoint;
      RhinoFormatPoint( p->m_point, sPoint );
      RhinoApp().Print( L"TextDot%d: point = (%s), text = \"%s\"\n",
  i, sPoint, p->m_text );
    }
  }

  return success;
}
```
