# Managing State in Flutter: Materials

This repo contains all the downloadable materials and projects associated with the **[Managing State in Flutter](https://www.raywenderlich.com/34552040-managing-state-in-flutter)** from [raywenderlich.com](https://www.raywenderlich.com).

Each edition has its own branch, named `versions/[VERSION]`. The default branch for this repo is for the most recent edition.

## Release History

| Branch                                                                                  | Version | Release Date |
| --------------------------------------------------------------------------------------- |:-------:|:------------:|
| [versions/2.0](https://github.com/raywenderlich/video-msf-materials/tree/versions/2.0) | 2.0     | 2022-09-22   |

### Understand State Management

<img width="300" alt="image" src="https://github.com/YamamotoDesu/ManagingState_in_Flutter/assets/47273077/a982f158-67be-42ee-9c85-631aa5aedc89">

lib/main.dart
```dart
void main() {
  runApp(const Application());
}

class Application extends StatefulWidget {
  const Application({super.key});

  @override
  State<Application> createState() => _ApplicationState();
}

class _ApplicationState extends State<Application> {
  final pillarData = Pillar(type: PillarType.flutter, articleCount: 115);
  late ValueNotifier<int> valueNoitifier;

  @override
  void initState() {
    valueNoitifier = ValueNotifier<int>(pillarData.articleCount);
    valueNoitifier.addListener(() {
      setState(() {
        final increaseAmount = valueNoitifier.value - pillarData.articleCount;
        pillarData.increaseArticleCount(by: increaseAmount);
      });
    });
    super.initState();
  }

  @override
  void dispose() {
    valueNoitifier.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tutorial Tracker',
      theme: ThemeData(primarySwatch: Colors.green),
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Tutorial Tracker'),
        ),
        body: TutorialsPage(
          pillar: pillarData,
          valueNotifier: valueNoitifier,
        ),
      ),
    );
  }
}
```

lib/pages/tutorials_page.dart
```dart
class TutorialsPage extends StatefulWidget {
  final Pillar pillar;
  final ValueNotifier<int> valueNotifier;
  const TutorialsPage({
    required this.pillar,
    required this.valueNotifier,
    super.key,
  });

  @override
  State<TutorialsPage> createState() => _TutorialsPageState();
}

class _TutorialsPageState extends State<TutorialsPage> {
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        Center(
          child: TutorialWidget(
            pillar: widget.pillar,
            valueNotifier: widget.valueNotifier,
          ),
        ),
        Padding(
          padding: const EdgeInsets.only(top: 24.0),
          child: Text(
            'Total Tutorials: ${widget.pillar.articleCount}',
            style: const TextStyle(fontSize: 30, fontWeight: FontWeight.bold),
          ),
        )
      ],
    );
  }
}
```

lib/widgets/tutorial_widget.dart
```dart
class TutorialWidget extends StatefulWidget {
  final Pillar pillar;
  final ValueNotifier<int> valueNotifier;
  const TutorialWidget({
    required this.pillar,
    required this.valueNotifier,
    super.key,
  });

  @override
  State<TutorialWidget> createState() => _TutorialWidgetState();
}

class _TutorialWidgetState extends State<TutorialWidget> {
  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        InkWell(
          onTap: () {
            setState(() {
              widget.valueNotifier.value++;
            });
          },
          child: Image.asset('assets/images/${widget.pillar.type.imageName}',
              width: 110, height: 110),
        ),
        Positioned(
          bottom: 2,
          child: CircleAvatar(
            backgroundColor: Colors.blue,
            child: Text(widget.pillar.articleCount.toString()),
          ),
        )
      ],
    );
  }
}
```
