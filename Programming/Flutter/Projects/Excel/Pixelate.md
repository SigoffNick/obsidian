import 'package:flutter/services.dart';  
import 'package:image/image.dart' as img;  
  
void main() {  
  runApp(const MyApp());  
}  
  
class MyApp extends StatelessWidget {  
  const MyApp({super.key});  
  
  @override  
  Widget build(BuildContext context) {    const width = 40;  
    const height = 40;  
    return MaterialApp(  
      home: Scaffold(        body: Center(          child: FutureBuilder(            future: pixelateImageAndGetColors(              imagePath: 'assets/image1.jpg',  
              verticalParts: height,  
              horizontalParts: width,  
              backgroundColor: Colors.white,  
              activeColor: Colors.black,  
            ),  
            builder: (context, snapshot) {  
              if (snapshot.connectionState == ConnectionState.done) {  
                List<List<Color>>? colorGrid = snapshot.data;  
                if (colorGrid == null) {  
                  return const Text('Something wrong');  
                }  
                return GridView.builder(  
                  gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(  
                    crossAxisCount: width,  
                    mainAxisSpacing: 2,  
                    crossAxisSpacing: 2,  
                  ),  
                  itemCount: width * height,  
                  itemBuilder: (context, index) {  
                    final x = index % width;  
                    final y = index ~/ height;  
                    return Container(color: colorGrid[y][x]);  
                  },  
                );  
              } else {  
                return CircularProgressIndicator();  
              }  
            },  
          ),  
        ),  
      ),  
    );  
  }  
  
  Future<List<List<Color>>?> pixelateImageAndGetColors({    required String imagePath,  
    required int horizontalParts,  
    required int verticalParts,  
    required Color backgroundColor,  
    required Color activeColor,  
    bool isAverage = false,  
  }) async {  
    final timer = Stopwatch()..start();  
  
    final ByteData data = await rootBundle.load(imagePath);  
  
    final img.Image image = img.decodeImage(data.buffer.asUint8List())!;  
  
    final int wPx = (image.width / horizontalParts).floor();  
    final int hPx = (image.height / verticalParts).floor();  
  
    final List<List<Color>> result = List.generate(verticalParts,  
        (y) => List.generate(horizontalParts, (x) => Colors.black));  
  
    try {  
      for (int y = 0; y < verticalParts; y++) {  
        for (int x = 0; x < horizontalParts; x++) {  
          await _checkTimer(timer);  
          int totalRed = 0;  
          int totalGreen = 0;  
          int totalBlue = 0;  
          int pixelCount = 0;  
  
          for (int dy = 0; dy < hPx; dy++) {  
            for (int dx = 0; dx < wPx; dx++) {  
              final int px = x * wPx + dx;  
              final int py = y * hPx + dy;  
              if (px < image.width && py < image.height) {  
                final int pixel = image.getPixel(px, py);  
                totalRed += img.getRed(pixel);  
                totalGreen += img.getGreen(pixel);  
                totalBlue += img.getBlue(pixel);  
                pixelCount++;  
              }  
            }          }          if (pixelCount == 0) {  
            continue;  
          }  
  
          final Color averageColor = Color.fromARGB(  
            255,  
            (totalRed / pixelCount).round(),  
            (totalGreen / pixelCount).round(),  
            (totalBlue / pixelCount).round(),  
          );  
          if (isAverage) {  
            result[y][x] = averageColor;  
          } else {  
            result[y][x] =                _getClosestColor(backgroundColor, activeColor, averageColor);  
          }  
        }      }      return result;  
    } catch (e) {  
      return null;  
    }  
  }  
  Future<void> _checkTimer(Stopwatch timer) async {  
    if (timer.elapsedMicroseconds > 16) {  
      await Future.delayed(Duration.zero);  
      timer.reset();  
    }  
  }  
  Color _getClosestColor(Color background, Color active, Color average) {  
    final double distanceToBackground =  
        _calculateColorDistance(average, background);  
    final double distanceToActive = _calculateColorDistance(average, active);  
  
    return distanceToBackground < distanceToActive ? background : active;  
  }  
  
  double _calculateColorDistance(Color c1, Color c2) {  
    return ((c1.red - c2.red) * (c1.red - c2.red) +  
            (c1.green - c2.green) * (c1.green - c2.green) +            (c1.blue - c2.blue) * (c1.blue - c2.blue))        .toDouble();  
  }  
}