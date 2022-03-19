# epub_view

This originally comes from https://github.com/ScerIO/packages.flutter, a developer named SergeShkurko seems to have done a lot of the commits 
(if not all of them.).  I am trying to understand why this widget will not import into flutterflow.io for some friends of mine. If this becomes 
useful, feel free to take whatever you need out of it.  (MIT License)

I am also working on putting a version of this in pub.dev at some point when I figure out how all of that works.


Pure flutter widget for view EPUB documents on all platforms. Based on [epub](https://pub.dev/packages/epub) package. Render with flutter widgets (not native view) on any platforms: **Web**, **MacOs**, **Windows** **Linux**, **Android** and **iOS**

## Showcase

<img width="50%" src="https://raw.githubusercontent.com/rbcprolabs/packages.flutter/master/packages/epub_view/example/media/example.gif?raw=true" />

## Getting Started

In your flutter project add the dependency:

[![pub package](https://img.shields.io/pub/v/epub_view.svg)](https://pub.dartlang.org/packages/epub_view)

```yaml
dependencies:
  epub_view: any
```

## Usage example:
```dart
import 'dart:typed_data';

import 'package:flutter/material.dart';
import 'package:flutter_epub/flutter_epub.dart';
import 'package:flutter/services.dart' show rootBundle;

Future<Uint8List> _loadFromAssets(String assetName) async {
  final bytes = await rootBundle.load(assetName);
  return bytes.buffer.asUint8List();
}

EpubController _epubController;

@override
void initState() {
  _epubController = EpubController(
    // Load document
    document: EpubReader.readBook(_loadFromAssets('assets/book.epub')),
    // Set start point
    epubCfi: 'epubcfi(/6/6[chapter-2]!/4/2/1612)',
  );
  super.initState();
}

@override
Widget build(BuildContext context) => Scaffold(
  appBar: AppBar(
    // Show actual chapter name
    title: EpubActualChapter(
      controller: _epubController,
      builder: (chapterValue) => Text(
        'Chapter ${chapterValue.chapter.Title ?? ''}',
        textAlign: TextAlign.start,
      ),
    ),
  ),
  // Show table of contents
  drawer: Drawer(
    child: EpubReaderTableOfContents(
      controller: _epubController,
    ),
  ),
  // Show epub document
  body: EpubView(
    controller: _epubController,
  ),
);
```

## How start from last view position?
This method allows you to keep the exact reading position even inside the chapter:
```dart
_epubController = EpubController(
  // initialize with epub cfi string for open book from last position
  epubCfi: 'epubcfi(/6/6[chapter-2]!/4/2/1612)',
);

// Attach controller
EpubView(
  controller: _epubController,
);

// Get epub cfi string
// for example output - epubcfi(/6/6[chapter-2]!/4/2/1612)
final cfi = _epubController.generateEpubCfi();

// or usage controller for navigate
_epubController.gotoEpubCfi('epubcfi(/6/6[chapter-2]!/4/2/1612)');
```

