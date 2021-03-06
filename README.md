# flutter_video_compress

Compressed video generates a new path, you can choose to keep the source video or delete it, and provide a function to get a thumbnail of the video file.

<p align="left">
  <a href="https://pub.dartlang.org/packages/flutter_video_compress"><img alt="pub version" src="https://img.shields.io/pub/v/flutter_video_compress.svg"></a>
  <img alt="license" src="https://img.shields.io/github/license/TenkaiRuri/flutter_video_compress.svg">
  <a href="https://github.com/TenkaiRuri/flutter_video_compress"><img alt="github stars" src="https://img.shields.io/github/stars/TenkaiRuri/flutter_video_compress.svg?style=social&label=Stars"></a>
</p>

## Methods
|Function|Parameter|Description|Return|
|--|--|--|--|
|getThumbnail|`String path`, `int quality`|Return a `thumbnail` of the video from the input file uri|`Uint8List` bitmap|
|compressVideo|`String path`, `bool deleteOrigin`|Compress the video file and return a `new path`|`String` path|

## Usage
**Creating instance.**
```dart
FlutterVideoCompress _flutterVideoCompress = FlutterVideoCompress();
```

**Get a video file thumbnail**
```dart
final Uint8List _image = await _flutterVideoCompress
  .getThumbnail(path: file.path, quality: 50)
```

**Compress a Video**
```dart
final String newPath = await _flutterVideoCompress
  .compressVideo(path: file.path, deleteOrigin: true);
  
print(newPath);
```

## example
```dart
import 'dart:io';
import 'dart:typed_data';

import 'package:flutter/material.dart';
import 'package:flutter_video_compress/flutter_video_compress.dart';
import 'package:image_picker/image_picker.dart' show ImagePicker, ImageSource;

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  FlutterVideoCompress _flutterVideoCompress = FlutterVideoCompress();
  Uint8List _image;

  Future<void> _videoPicker() async {
    File file = await ImagePicker.pickVideo(source: ImageSource.camera);
    if (file != null && mounted) {
      _image = await _flutterVideoCompress
          .getThumbnail(path: file.path, quality: 50)
          .whenComplete(() {
        setState(() {});
      });
      final String newPath = await _flutterVideoCompress.compressVideo(
          path: file.path, deleteOrigin: true);
      print(newPath);
    }
  }

  List<Widget> _builColumnChildren() {
    // dart 2.3 before
    final _list = <Widget>[
      FlatButton(child: Text('take video'), onPressed: _videoPicker)
    ];
    if (_image != null) {
      _list.add(Flexible(child: Image.memory(_image)));
    }
    return _list;

    // dart 2.3
    // final _list =  [
    //   FlatButton(child: Text('take video'), onPressed: _videoPicker),
    //   if(_image != null) Flexible(child: Image.memory(_image))
    // ];
    // return _list;
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('Plugin example app')),
        body: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: _builColumnChildren(),
        ),
      ),
    );
  }
}
```