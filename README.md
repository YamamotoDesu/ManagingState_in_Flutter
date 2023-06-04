# Managing State in Flutter: Materials

This repo contains all the downloadable materials and projects associated with the **[Managing State in Flutter](https://www.raywenderlich.com/34552040-managing-state-in-flutter)** from [raywenderlich.com](https://www.raywenderlich.com).

Each edition has its own branch, named `versions/[VERSION]`. The default branch for this repo is for the most recent edition.

## Release History

| Branch                                                                                  | Version | Release Date |
| --------------------------------------------------------------------------------------- |:-------:|:------------:|
| [versions/2.0](https://github.com/raywenderlich/video-msf-materials/tree/versions/2.0) | 2.0     | 2022-09-22   |

-----

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

------
### Extend a Value Notifier

lib/models/pillar.dart
```dart
class Pillar extends ValueNotifier<int>{
  int get articleCount => value;
  var active = true;
  final PillarType type;

  Pillar({required this.type, int articleCount = 10}) : super(articleCount); 

  void increaseArticleCount({int by = 1}) {
    value += by;
  }
}
```

lib/main.dart
```dart
class _ApplicationState extends State<Application> {
  final pillarData = Pillar(type: PillarType.flutter, articleCount: 115);

  @override
  void dispose() {
    pillarData.dispose();
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
        body: ValueListenableBuilder(
          valueListenable: pillarData,
          builder: (context, value, child) {
            return TutorialsPage(
              pillar: pillarData,
            );
          }
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
  const TutorialsPage({
    required this.pillar,
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

  const TutorialWidget({
    required this.pillar,
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
            widget.pillar.increaseArticleCount();
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

### Mutate the Inherited Widget

lib/state/pillar_widget.dart
```dart
import 'package:flutter/material.dart';
import '../models/pillar.dart';

class PillarIhheritedWiget extends InheritedWidget {
  final PillarState state;
  final int articleCount;

  const PillarIhheritedWiget(
      {required this.articleCount, required this.state, super.key, required super.child});

  static PillarState of(BuildContext context) {
    final PillarIhheritedWiget? result =
        context.dependOnInheritedWidgetOfExactType<PillarIhheritedWiget>();
    assert(result != null, 'No PillarWidget found in context');
    return result!.state;
  }

  @override
  bool updateShouldNotify(PillarIhheritedWiget old) => articleCount != old.articleCount;
}

class PillarSttatefulWidget extends StatefulWidget {
  final Widget child;
  final Pillar pillarData;
  const PillarSttatefulWidget(
      {required this.pillarData, required this.child, Key? key})
      : super(key: key);

  @override
  State<PillarSttatefulWidget> createState() => PillarState();
}

class PillarState extends State<PillarSttatefulWidget> {

  get articleCount => widget.pillarData.articleCount;
  get imageName => widget.pillarData.type.imageName;

  void increaseArticleCount({ int by = 1}) {
    setState(() {
      widget.pillarData.increaseArticleCount(by: by);
    });
  }

  @override
  Widget build(BuildContext context) {
    return PillarIhheritedWiget(
      articleCount: articleCount,
      state: this,
      child: widget.child,
    );
  }
}
```

lib/models/pillar.dart
```dart
import 'package:flutter/material.dart';

class Pillar {
  var _articleCount = 0;
  int get articleCount => _articleCount;
  var active = true;
  final PillarType type;

  Pillar({required this.type, int articleCount = 10}) {
    _articleCount = articleCount;
  } 

  void increaseArticleCount({int by = 1}) {
    _articleCount += by;
  }
}

enum PillarType {
  flutter('flutter.png', Colors.blue),
  android('android.png', Colors.green),
  ios('ios.png', Colors.orange);

  final String imageName;
  final Color backgroundColor;

  const PillarType(this.imageName, this.backgroundColor);
}

```

lib/main.dart
```dart
import 'package:flutter/material.dart';
import 'pages/tutorials_page.dart';
import 'models/pillar.dart';
import 'state/pillar_widget.dart';

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

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tutorial Tracker',
      theme: ThemeData(primarySwatch: Colors.green),
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Tutorial Tracker'),
        ),
        body: PillarSttatefulWidget(
            pillarData: pillarData,
            child: const TutorialsPage(),
          ),
      ),
    );
  }
}
```

lib/pages/tutorials_page.dart
```dart
import 'package:flutter/material.dart';
import '../widgets/tutorial_widget.dart';
import '../state/pillar_widget.dart';

class TutorialsPage extends StatefulWidget {
  const TutorialsPage({
    super.key,
  });

  @override
  State<TutorialsPage> createState() => _TutorialsPageState();
}

class _TutorialsPageState extends State<TutorialsPage> {
  @override
  Widget build(BuildContext context) {
    final pillar = PillarIhheritedWiget.of(context);
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        const Center(
          child: TutorialWidget()),
        Padding(
          padding: const EdgeInsets.only(top: 24.0),
          child: Text(
            'Total Tutorials: ${pillar.articleCount}',
            style: const TextStyle(fontSize: 30, fontWeight: FontWeight.bold),
          ),
        )
      ],
    );
  }
}
```

## Use Provider

lib/models/pillar.dart
```dart
import 'package:flutter/material.dart';

class Pillar extends ChangeNotifier {
  var _articleCount = 0;
  int get articleCount => _articleCount;
  var active = true;
  final PillarType type;

  Pillar({required this.type, int articleCount = 10}) {
    _articleCount = articleCount;
  } 

  void increaseArticleCount({int by = 1}) {
    _articleCount += by;
    notifyListeners();
  }
}

enum PillarType {
  flutter('flutter.png', Colors.blue),
  android('android.png', Colors.green),
  ios('ios.png', Colors.orange);

  final String imageName;
  final Color backgroundColor;

  const PillarType(this.imageName, this.backgroundColor);
}
```

lib/main.dart
```dart
import 'package:flutter/material.dart';
import 'pages/tutorials_page.dart';
import 'models/pillar.dart';
import 'package:provider/provider.dart';

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

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Tutorial Tracker',
      theme: ThemeData(primarySwatch: Colors.green),
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Tutorial Tracker'),
        ),
        body: ChangeNotifierProvider<Pillar>(
          create: (context) => pillarData,
          child: const TutorialsPage(),
        ),
      ),
    );
  }
}
```

lib/pages/tutorials_page.dart
```dart
import 'package:flutter/material.dart';
import '../widgets/tutorial_widget.dart';
import 'package:provider/provider.dart';
import '../models/pillar.dart';

class TutorialsPage extends StatefulWidget {
  const TutorialsPage({
    super.key,
  });

  @override
  State<TutorialsPage> createState() => _TutorialsPageState();
}

class _TutorialsPageState extends State<TutorialsPage> {
  @override
  Widget build(BuildContext context) {
    final pillar = Provider.of<Pillar>(context);
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        const Center(
          child: TutorialWidget()),
        Padding(
          padding: const EdgeInsets.only(top: 24.0),
          child: Text(
            'Total Tutorials: ${pillar.articleCount}',
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
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../models/pillar.dart';

class TutorialWidget extends StatefulWidget {

  const TutorialWidget({
    super.key,
  });

  @override
  State<TutorialWidget> createState() => _TutorialWidgetState();
}

class _TutorialWidgetState extends State<TutorialWidget> {
  @override
  Widget build(BuildContext context) {
    final pillar = Provider.of<Pillar>(context);
    return Stack(
      children: [
        InkWell(
          onTap: () {
            pillar.increaseArticleCount();
          },
          child: Image.asset('assets/images/${pillar.type.imageName}',
              width: 110, height: 110),
        ),
        Positioned(
          bottom: 2,
          child: CircleAvatar(
            backgroundColor: Colors.blue,
            child: Text(pillar.articleCount.toString()),
          ),
        )
      ],
    );
  }
}
```
