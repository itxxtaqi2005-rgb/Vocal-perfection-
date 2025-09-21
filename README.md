// A complete, single-file Flutter app to serve as a scaffolding
// for a vocal practice and naat khawani application.

import 'dart:async';
import 'dart:io';
import 'dart:math';
import 'package:flutter/material.dart';

// --- Global App Theme and Constants ---
const Color primaryColor = Color(0xFF81B29A); // Soft green
const Color accentColor = Color(0xFFF2CC8F); // Soft yellow
const Color backgroundColor = Color(0xFFE0E0E0); // Light grey
const Color cardColor = Color(0xFFFFFFFF); // White
const Color offWhite = Color(0xFFF4F4F4); // Off-white
const Color darkGrey = Color(0xFF333333); // Dark grey

const double borderRadius = 20.0;

final ThemeData appTheme = ThemeData(
  primaryColor: primaryColor,
  scaffoldBackgroundColor: backgroundColor,
  appBarTheme: const AppBarTheme(
    backgroundColor: backgroundColor,
    elevation: 0,
    centerTitle: true,
    titleTextStyle: TextStyle(
      color: darkGrey,
      fontSize: 22,
      fontWeight: FontWeight.bold,
    ),
  ),
  bottomNavigationBarTheme: const BottomNavigationBarThemeData(
    backgroundColor: cardColor,
    selectedItemColor: primaryColor,
    unselectedItemColor: Colors.grey,
    selectedLabelStyle: TextStyle(fontWeight: FontWeight.bold),
  ),
  elevatedButtonTheme: ElevatedButtonThemeData(
    style: ElevatedButton.styleFrom(
      foregroundColor: darkGrey,
      backgroundColor: accentColor,
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(borderRadius),
      ),
      padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 12),
      textStyle: const TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
    ),
  ),
  cardTheme: CardTheme(
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(borderRadius),
    ),
    elevation: 4,
    color: cardColor,
    margin: const EdgeInsets.all(16),
  ),
  sliderTheme: SliderThemeData(
    activeTrackColor: primaryColor,
    inactiveTrackColor: primaryColor.withOpacity(0.3),
    thumbColor: primaryColor,
    overlayColor: primaryColor.withOpacity(0.2),
  ),
);

// --- Entry Point ---
void main() {
  runApp(const VocalPracticeApp());
}

// --- Main App Widget ---
class VocalPracticeApp extends StatefulWidget {
  const VocalPracticeApp({super.key});

  @override
  State<VocalPracticeApp> createState() => _VocalPracticeAppState();
}

class _VocalPracticeAppState extends State<VocalPracticeApp> {
  int _selectedIndex = 0;
  bool _isUrdu = true;

  void _onItemTapped(int index) {
    setState(() {
      _selectedIndex = index;
    });
  }

  // Define titles and widgets for each tab
  static const List<String> _englishTitles = [
    'Practice',
    'Scale Trainer',
    'Beat Guide',
    'Library',
    'Progress'
  ];
  static const List<String> _urduTitles = [
    'مشق',
    'اسکیل ٹرینر',
    'تال گائیڈ',
    'لائبریری',
    'پیش رفت'
  ];

  late final List<Widget> _widgetOptions = <Widget>[
    PracticeModePage(
      isUrdu: _isUrdu,
      onLanguageToggle: _toggleLanguage,
    ),
    ScaleTrainerPage(isUrdu: _isUrdu),
    BeatGuidePage(isUrdu: _isUrdu),
    LibraryPage(isUrdu: _isUrdu),
    ProgressTrackerPage(isUrdu: _isUrdu),
  ];

  void _toggleLanguage() {
    setState(() {
      _isUrdu = !_isUrdu;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Vocal Practice App',
      theme: appTheme,
      home: Scaffold(
        appBar: AppBar(
          title: Text(_isUrdu ? 'وکل پریکٹس ایپ' : 'Vocal Practice App'),
          actions: [
            IconButton(
              icon: const Icon(Icons.language),
              onPressed: _toggleLanguage,
            ),
          ],
        ),
        body: Center(
          child: _widgetOptions.elementAt(_selectedIndex),
        ),
        bottomNavigationBar: Container(
          decoration: const BoxDecoration(
            borderRadius: BorderRadius.vertical(top: Radius.circular(30)),
            color: cardColor,
            boxShadow: [
              BoxShadow(
                color: Colors.black12,
                blurRadius: 10,
                offset: Offset(0, -5),
              ),
            ],
          ),
          child: ClipRRect(
            borderRadius: const BorderRadius.vertical(top: Radius.circular(30)),
            child: BottomNavigationBar(
              items: [
                BottomNavigationBarItem(
                  icon: const Icon(Icons.mic),
                  label: _isUrdu ? _urduTitles[0] : _englishTitles[0],
                ),
                BottomNavigationBarItem(
                  icon: const Icon(Icons.piano),
                  label: _isUrdu ? _urduTitles[1] : _englishTitles[1],
                ),
                BottomNavigationBarItem(
                  icon: const Icon(Icons.access_time),
                  label: _isUrdu ? _urduTitles[2] : _englishTitles[2],
                ),
                BottomNavigationBarItem(
                  icon: const Icon(Icons.library_music),
                  label: _isUrdu ? _urduTitles[3] : _englishTitles[3],
                ),
                BottomNavigationBarItem(
                  icon: const Icon(Icons.bar_chart),
                  label: _isUrdu ? _urduTitles[4] : _englishTitles[4],
                ),
              ],
              currentIndex: _selectedIndex,
              onTap: _onItemTapped,
            ),
          ),
        ),
      ),
    );
  }
}

// --- 1. Practice Mode Widget ---
class PracticeModePage extends StatefulWidget {
  final bool isUrdu;
  final VoidCallback onLanguageToggle;

  const PracticeModePage({
    super.key,
    required this.isUrdu,
    required this.onLanguageToggle,
  });

  @override
  State<PracticeModePage> createState() => _PracticeModePageState();
}

class _PracticeModePageState extends State<PracticeModePage> {
  bool _isRecording = false;
  bool _isPlaying = false;
  double _pitch = 0.0;
  double _playbackSpeed = 1.0;

  // Placeholder for real-time pitch detection
  Timer? _pitchDetectionTimer;
  Timer? _recordingTimer;

  void _startPitchDetection() {
    // NOTE: This is a placeholder. You would implement a real pitch detection
    // library here (e.g., using `pitch_detector_dart` or a TFLite model).
    setState(() {
      _isRecording = true;
    });
    // Simulate real-time pitch updates
    _pitchDetectionTimer = Timer.periodic(const Duration(milliseconds: 100), (timer) {
      setState(() {
        _pitch = (sin(timer.tick / 10.0) * 100).abs(); // Mock pitch value
      });
    });

    // Simulate recording duration
    _recordingTimer = Timer(const Duration(seconds: 10), () {
      _stopRecording();
    });
  }

  void _stopRecording() {
    setState(() {
      _isRecording = false;
      _pitchDetectionTimer?.cancel();
      _recordingTimer?.cancel();
      _pitch = 0.0;
    });
    // NOTE: Here you would save the recording and analyze it.
    _showFeedbackDialog();
  }

  void _playRecording() {
    // NOTE: This is a placeholder. Use `just_audio` or similar for real playback.
    setState(() {
      _isPlaying = true;
    });
    // Simulate playback duration
    Timer(const Duration(seconds: 5), () {
      setState(() {
        _isPlaying = false;
      });
    });
  }

  void _showFeedbackDialog() {
    // NOTE: The AI analysis would be done here.
    final feedbackText = widget.isUrdu
        ? 'آواز میں کچھ تبدیلی ہے، جس کی وجہ سے یہ آف پِچ لگ رہا ہے۔ مزید مشق کریں۔'
        : 'Some slight variations in pitch were detected. Keep practicing!';

    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(borderRadius),
          ),
          title: Text(widget.isUrdu ? 'آئی فیڈ بیک' : 'AI Feedback'),
          content: Text(feedbackText),
          actions: [
            TextButton(
              child: Text(widget.isUrdu ? 'ٹھیک ہے' : 'OK'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

  @override
  void dispose() {
    _pitchDetectionTimer?.cancel();
    _recordingTimer?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          // Real-time Pitch Visualizer (Placeholder)
          Container(
            height: 150,
            width: double.infinity,
            decoration: BoxDecoration(
              color: offWhite,
              borderRadius: BorderRadius.circular(borderRadius),
              border: Border.all(color: primaryColor, width: 2),
            ),
            child: Center(
              child: Text(
                _isRecording
                    ? '${widget.isUrdu ? 'پچ' : 'Pitch'}: ${_pitch.toStringAsFixed(2)} Hz'
                    : (widget.isUrdu ? 'ریکارڈ کرنے کے لیے دبائیں' : 'Press to Record'),
                style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold, color: darkGrey),
              ),
            ),
          ),
          const SizedBox(height: 32),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: [
              // Record Button
              ElevatedButton(
                onPressed: _isRecording ? _stopRecording : _startPitchDetection,
                style: ElevatedButton.styleFrom(
                  backgroundColor: _isRecording ? Colors.red.shade400 : accentColor,
                ),
                child: Icon(
                  _isRecording ? Icons.stop : Icons.mic,
                  size: 30,
                  color: darkGrey,
                ),
              ),
              // Playback Button
              ElevatedButton(
                onPressed: _isRecording || _isPlaying ? null : _playRecording,
                style: ElevatedButton.styleFrom(
                  backgroundColor: _isPlaying ? Colors.grey.shade400 : accentColor,
                ),
                child: Icon(
                  _isPlaying ? Icons.pause : Icons.play_arrow,
                  size: 30,
                  color: darkGrey,
                ),
              ),
            ],
          ),
          const SizedBox(height: 32),
          Card(
            child: Padding(
              padding: const EdgeInsets.all(16.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    widget.isUrdu ? 'پلے بیک اسپیڈ' : 'Playback Speed',
                    style: const TextStyle(fontWeight: FontWeight.bold),
                  ),
                  Slider(
                    value: _playbackSpeed,
                    min: 0.5,
                    max: 2.0,
                    divisions: 3,
                    label: _playbackSpeed.toStringAsFixed(1),
                    onChanged: (double value) {
                      setState(() {
                        _playbackSpeed = value;
                      });
                    },
                  ),
                  Center(
                    child: Text(
                      '${widget.isUrdu ? 'اسپیڈ' : 'Speed'}: ${_playbackSpeed.toStringAsFixed(1)}x',
                      style: const TextStyle(fontSize: 16),
                    ),
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}

// --- 2. Scale Trainer Widget ---
class ScaleTrainerPage extends StatelessWidget {
  final bool isUrdu;

  const ScaleTrainerPage({super.key, required this.isUrdu});

  // Placeholder function for playing a note
  void _playNote(String note) {
    // NOTE: This is a placeholder. Use an audio library to play the note.
    print('Playing note: $note');
  }

  @override
  Widget build(BuildContext context) {
    // Mock notes for a simple scale
    final List<String> notes = isUrdu
        ? ['سا', 'رے', 'گا', 'ما', 'پا', 'دھا', 'نی', 'سا']
        : ['Sa', 'Re', 'Ga', 'Ma', 'Pa', 'Dha', 'Ni', 'Sa'];

    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Column(
        children: [
          Card(
            child: Padding(
              padding: const EdgeInsets.all(16.0),
              child: Column(
                children: [
                  Text(
                    isUrdu ? 'اپنی آواز کی رینج معلوم کریں' : 'Find Your Vocal Range',
                    style: const TextStyle(fontWeight: FontWeight.bold, fontSize: 18),
                  ),
                  const SizedBox(height: 8),
                  Text(
                    isUrdu
                        ? 'کم سے کم سے زیادہ سے زیادہ آرام دہ نوٹ گائیں'
                        : 'Sing from your lowest to highest comfortable note',
                    textAlign: TextAlign.center,
                  ),
                  const SizedBox(height: 16),
                  // Placeholder for vocal range detection
                  Container(
                    height: 100,
                    width: double.infinity,
                    decoration: BoxDecoration(
                      color: offWhite,
                      borderRadius: BorderRadius.circular(borderRadius),
                    ),
                    child: Center(
                      child: Text(
                        isUrdu ? 'اپ کی رینج: B2 - G4' : 'Your Range: B2 - G4',
                        style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                      ),
                    ),
                  ),
                ],
              ),
            ),
          ),
          const SizedBox(height: 16),
          Expanded(
            child: Card(
              child: Padding(
                padding: const EdgeInsets.all(16.0),
                child: Column(
                  children: [
                    Text(
                      isUrdu ? 'اسکیل ٹرینر' : 'Scale Trainer',
                      style: const TextStyle(fontWeight: FontWeight.bold, fontSize: 18),
                    ),
                    const SizedBox(height: 16),
                    // Piano Notes
                    Expanded(
                      child: GridView.builder(
                        physics: const NeverScrollableScrollPhysics(),
                        gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
                          crossAxisCount: 4,
                          childAspectRatio: 2.5,
                          crossAxisSpacing: 8,
                          mainAxisSpacing: 8,
                        ),
                        itemCount: notes.length,
                        itemBuilder: (context, index) {
                          return ElevatedButton(
                            onPressed: () => _playNote(notes[index]),
                            child: Text(notes[index]),
                          );
                        },
                      ),
                    ),
                  ],
                ),
              ),
            ),
          ),
        ],
      ),
    );
  }
}

// --- 3. Beat / Taal Guide Widget ---
class BeatGuidePage extends StatefulWidget {
  final bool isUrdu;

  const BeatGuidePage({super.key, required this.isUrdu});

  @override
  State<BeatGuidePage> createState() => _BeatGuidePageState();
}

class _BeatGuidePageState extends State<BeatGuidePage> {
  bool _isRunning = false;
  int _bpm = 60;
  Timer? _metronomeTimer;
  int _currentBeat = 0;

  void _startMetronome() {
    _metronomeTimer?.cancel();
    setState(() {
      _isRunning = true;
    });

    final int intervalMs = (60000 / _bpm).round();
    _metronomeTimer = Timer.periodic(Duration(milliseconds: intervalMs), (timer) {
      setState(() {
        _currentBeat = (_currentBeat + 1) % 4; // Simple 4-beat cycle
      });
      // NOTE: Play a sound here.
      print('Beat! current beat: $_currentBeat');
    });
  }

  void _stopMetronome() {
    _metronomeTimer?.cancel();
    setState(() {
      _isRunning = false;
      _currentBeat = 0;
    });
  }

  @override
  void dispose() {
    _metronomeTimer?.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Card(
        child: Padding(
          padding: const EdgeInsets.all(16.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                widget.isUrdu ? 'بی پی ایم' : 'BPM',
                style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 8),
              Text(
                _bpm.toString(),
                style: TextStyle(
                  fontSize: 80,
                  fontWeight: FontWeight.bold,
                  color: primaryColor,
                ),
              ),
              Slider(
                value: _bpm.toDouble(),
                min: 40,
                max: 180,
                divisions: 140,
                label: _bpm.toString(),
                onChanged: (double value) {
                  setState(() {
                    _bpm = value.round();
                    if (_isRunning) {
                      _startMetronome(); // Restart with new tempo
                    }
                  });
                },
              ),
              const SizedBox(height: 16),
              // Beat indicators
              Row(
                mainAxisAlignment: MainAxisAlignment.center,
                children: List.generate(4, (index) {
                  return Container(
                    margin: const EdgeInsets.symmetric(horizontal: 4),
                    width: 20,
                    height: 20,
                    decoration: BoxDecoration(
                      color: _isRunning && _currentBeat == index
                          ? accentColor
                          : primaryColor.withOpacity(0.3),
                      shape: BoxShape.circle,
                    ),
                  );
                }),
              ),
              const SizedBox(height: 32),
              ElevatedButton(
                onPressed: _isRunning ? _stopMetronome : _startMetronome,
                style: ElevatedButton.styleFrom(
                  padding: const EdgeInsets.symmetric(horizontal: 40, vertical: 20),
                  backgroundColor: _isRunning ? Colors.red.shade400 : primaryColor,
                  shape: const CircleBorder(),
                ),
                child: Icon(
                  _isRunning ? Icons.pause : Icons.play_arrow,
                  size: 40,
                  color: cardColor,
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

// --- 4. Library Widget (Placeholder) ---
class LibraryPage extends StatelessWidget {
  final bool isUrdu;

  const LibraryPage({super.key, required this.isUrdu});

  // Mock data for library
  final List<Map<String, String>> mockNaats = const [
    {'title': 'نعت نمبر 1', 'singer': 'فرضی گلوکار ا'},
    {'title': 'نعت نمبر 2', 'singer': 'فرضی گلوکار ب'},
  ];

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Padding(
          padding: const EdgeInsets.all(16.0),
          child: ElevatedButton.icon(
            onPressed: () {
              // NOTE: Implement file picker here for real use.
              print('Uploading file...');
            },
            icon: const Icon(Icons.upload_file),
            label: Text(isUrdu ? 'نعت اپ لوڈ کریں' : 'Upload Naat'),
          ),
        ),
        Expanded(
          child: ListView.builder(
            itemCount: mockNaats.length,
            itemBuilder: (context, index) {
              final naat = mockNaats[index];
              return Card(
                child: ListTile(
                  leading: c
