// ignore_for_file: unnecessary_cast, library_private_types_in_public_api, use_build_context_synchronously

import 'dart:math';

import 'package:flutter/material.dart';
import 'package:flutter_localizations/flutter_localizations.dart';
import 'package:table_calendar/table_calendar.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return
    MaterialApp(
      title: 'Agenda',
      theme: ThemeData.dark(),
      home: const TimePlannerScreen(),
      localizationsDelegates: const [
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
        GlobalCupertinoLocalizations.delegate,
     ],
      supportedLocales: const [
        Locale('pt'),
      ],
    );
  }
}

getRandomColor() {
  final Random random = Random();
  return Color.fromARGB(
    255,
    random.nextInt(256),
    random.nextInt(256),
    random.nextInt(256),
  );
}

class Event {
  String title;
  DateTime startTime;
  DateTime endTime;
  Color color;
  String location;

  Color getRandomColor() {
    final Random random = Random();
    return Color.fromARGB(
      255,
      random.nextInt(256),
      random.nextInt(256),
      random.nextInt(256),
    );
  }

  Event({
    required this.title,
    required this.startTime,
    required this.endTime,
    required this.color, 
    required this.location,
  });

  bool isMultiDayEvent() {
    return startTime.day != endTime.day || startTime.month
    != endTime.month || startTime.year != endTime.year;
  }
}

class TimePlannerScreen extends StatefulWidget {
  const TimePlannerScreen({super.key});
  @override
  _TimePlannerScreenState createState() => _TimePlannerScreenState();
}

class _TimePlannerScreenState extends State<TimePlannerScreen> {
  List<Event> events = [];
  final CalendarFormat _calendarFormat = CalendarFormat.week;
  DateTime _focusedDay = DateTime.now();
  DateTime? _selectedDay;
  Map<DateTime, List<Event>> _eventsByDay = {};

  @override
  Widget build(BuildContext context) {
    _eventsByDay = _groupEventsByDay(events);

    return Scaffold(
      appBar: AppBar(
        title: const Text('Agenda'),
      ),
      body: Column(
        children: [
          Container(
            padding: const EdgeInsets.all(8.0),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                const Text(
                  'Data selecionada:',
                  style: TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(width: 10),
                ElevatedButton(
                  onPressed: () async {
                    final DateTime? pickedDate = await showDatePicker(
                      context: context,
                      initialDate: _focusedDay,
                      firstDate: DateTime(2023),
                      lastDate: DateTime(2101),
                    );
                    if (pickedDate != null) {
                      setState(() {
                      _focusedDay = pickedDate;
                      _selectedDay = pickedDate;
                      });
                    }
                  },
                  child: Text(
                    _selectedDay != null
                    ? '${_selectedDay!.day.toString().padLeft(2, '0')}-${_selectedDay!.month.toString().padLeft(2, '0')}-${_selectedDay!.year}'
                    : 'Selecione uma data',
                  ),
                ),
              ],
            ),
          ),
          TableCalendar<Event>(
            locale: 'pt',
            firstDay: DateTime(2023),
            lastDay: DateTime(2101),
            focusedDay: _focusedDay,
            calendarFormat: _calendarFormat,
            selectedDayPredicate: (day) {
              return isSameDay(_selectedDay, (day) as DateTime?);
            },
            eventLoader: (day) {
              return _eventsByDay[day] ?? [];
            },
            onDaySelected: (selectedDay, focusedDay) {
              setState(() {
                _selectedDay = selectedDay;
                _focusedDay = focusedDay;
              });
            },
            onPageChanged: (focusedDay) {
              _focusedDay = focusedDay;
            },
            headerStyle: const HeaderStyle(
              formatButtonVisible: false,
            ),
          ),
          Expanded(
            child: Padding(
              padding: const EdgeInsets.all(8.0),
              child: events.isEmpty
              ? const Center(
                child: Text(
                  'Adicione um evento\npressionando o botão abaixo.',
                  textAlign: TextAlign.center,
                  style: TextStyle(fontSize: 20),
                ),
              )
              : ListView.builder(
                  itemCount: events.length,
                  itemBuilder: (context, index) {
                  final event = events[index];
                  if (event.startTime.isBefore(_selectedDay!.add(const Duration(days: 1))) &&
                      event.endTime.isAfter(_selectedDay!))
                  {
                    return Padding(
                      padding: const EdgeInsets.symmetric(vertical: 4.0, horizontal: 8.0),
                      child: Container(
                        decoration: BoxDecoration(
                          borderRadius: BorderRadius.circular(12.0),
                          color: event.color,
                        ),
                        child: ListTile(
                          title: Text(
                            event.title,
                            style: const TextStyle(
                              color: Colors.black,
                              fontSize: 18,
                            ),
                          ),
                          subtitle: Column(
                            crossAxisAlignment: CrossAxisAlignment.start,
                            children: [
                              Text(
                                '📍: ${event.location}',
                                style: const TextStyle(
                                  color: Colors.black,
                                  fontWeight: FontWeight.bold,
                                ),
                              ),
                              RichText(
                                text: TextSpan(
                                  text: 'Hora de Início: ',
                                  style: const TextStyle(
                                    color: Colors.black,
                                    fontSize: 14,
                                    fontWeight: FontWeight.normal,
                                  ),
                                  children: <TextSpan>[
                                    TextSpan(
                                      text: _formatTime(event.startTime),
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                    const TextSpan(
                                      text: '\nHora de Término: ',
                                    ),
                                    TextSpan(
                                      text: _formatTime(event.endTime),
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                  ],
                                ),
                              ),
                              if (event.isMultiDayEvent())
                              RichText(
                                text: TextSpan(
                                  text: 'Data de Início: ',
                                  style: const TextStyle(
                                    color: Colors.black,
                                    fontSize: 14,
                                    fontWeight: FontWeight.normal,
                                  ),
                                  children: <TextSpan>[
                                    TextSpan(
                                      text: _formatDate(event.startTime),
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                    const TextSpan(
                                      text: '\nData de Término: ',
                                    ),
                                    TextSpan(
                                      text: _formatDate(event.endTime),
                                      style: const TextStyle(
                                        fontWeight: FontWeight.bold,
                                      ),
                                    ),
                                  ],
                                ),
                              ),
                            ],
                          ),
                          trailing: IconButton(
                            icon: const Icon(Icons.delete),
                            onPressed: () {
                              setState(() {
                                events.remove(event);
                              });
                            },
                          ),
                          onTap: () async {
                            final editedEvent = await Navigator.push(
                              context,
                              MaterialPageRoute(
                                builder: (context) => EditEventScreen(event: event),
                              ),
                            );
                            if (editedEvent != null && editedEvent is Event) {
                              setState(() {
                                events[events.indexOf(event)] = editedEvent;
                              });
                            }
                          },
                        ),
                      ),
                    );
                  } 
                  else {
                    return const SizedBox();
                  }
                },
              ),
            ),
          ),
        ],
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          final randomColor = getRandomColor();
          Navigator.push(
            context,
            MaterialPageRoute(
              builder: (context) => AddEventScreen(randomColor: randomColor),
            ),
          ).then((value) {
            if (value != null && value is Event) {
              setState(() {
                events.add(value);
              });
            }
          });
        },
        child: const Icon(Icons.add),
      ),
    );
  }

  Map<DateTime, List<Event>> _groupEventsByDay(List<Event> events) {
    Map<DateTime, List<Event>> groupedEvents = {};

    for (var event in events) {
      DateTime date = DateTime(event.startTime.year, event.startTime.month, event.startTime.day);
      if (groupedEvents.containsKey(date)) {
        groupedEvents[date]!.add(event);
      } 
      else {
        groupedEvents[date] = [event];
      }
    }
    return groupedEvents;
  }

  String _formatTime(DateTime dateTime) {
    return '${dateTime.hour.toString().padLeft(2, '0')}:${dateTime.minute.toString().padLeft(2, '0')}';
  }

  String _formatDate(DateTime dateTime) {
    return '${dateTime.day.toString().padLeft(2, '0')}-${dateTime.month.toString().padLeft(2, '0')}-${dateTime.year}';
  }

  Color getRandomColor() {
    final Random random = Random();
    return Color.fromARGB(
      255,
      random.nextInt(256),
      random.nextInt(256),
      random.nextInt(256),
    );
  }
}

class AddEventScreen extends StatefulWidget {
  final Color randomColor;

  const AddEventScreen({Key? key, required this.randomColor}) : super(key: key);

  @override
  _AddEventScreenState createState() => _AddEventScreenState();
}

class _AddEventScreenState extends State<AddEventScreen> {
  late TextEditingController titleController;
  late DateTime selectedStartTime;
  late DateTime selectedEndTime;
  late String location;

  @override
  void initState() {
    super.initState();
    titleController = TextEditingController();
    selectedStartTime = DateTime.now();
    selectedEndTime = DateTime.now();
    location = '';
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Adicionar Evento'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              controller: titleController,
              decoration: const InputDecoration(labelText: 'Título do Evento'),
            ),
            const SizedBox(height: 20),
            TextField(
              onChanged: (value) {
                setState(() {
                  location = value;
                });
              },
              decoration: const InputDecoration(labelText: 'Localização do Evento'),
            ),
            const SizedBox(height: 20),
            TextButton(
              onPressed: () async {
                final DateTime? pickedStartTime = await showDatePicker(
                  context: context,
                  initialDate: selectedStartTime,
                  firstDate: DateTime(2023),
                  lastDate: DateTime(2101),
                );
                if (pickedStartTime != null) {
                  final TimeOfDay? pickedStartTimeOfDay = await showTimePicker(
                    context: context,
                    initialTime: TimeOfDay.fromDateTime(selectedStartTime),
                  );
                  if (pickedStartTimeOfDay != null) {
                    setState(() {
                      selectedStartTime = DateTime(
                        pickedStartTime.year,
                        pickedStartTime.month,
                        pickedStartTime.day,
                        pickedStartTimeOfDay.hour,
                        pickedStartTimeOfDay.minute,
                      );
                      if (selectedStartTime.isAfter(selectedEndTime)) {
                        selectedEndTime = selectedStartTime;
                      }
                    });
                  }
                }
              },
              child: Text('Hora de Início: ${_formatDateTime(selectedStartTime)}'),
            ),
            const SizedBox(height: 10),
            TextButton(
              onPressed: () async {
                final DateTime? pickedEndTime = await showDatePicker(
                  context: context,
                  initialDate: selectedEndTime,
                  firstDate: DateTime(2023),
                  lastDate: DateTime(2101),
                );
                if (pickedEndTime != null) {
                  final TimeOfDay? pickedEndTimeOfDay = await showTimePicker(
                    context: context,
                    initialTime: TimeOfDay.fromDateTime(selectedEndTime),
                  );
                  if (pickedEndTimeOfDay != null) {
                    setState(() {
                      selectedEndTime = DateTime(
                        pickedEndTime.year,
                        pickedEndTime.month,
                        pickedEndTime.day,
                        pickedEndTimeOfDay.hour,
                        pickedEndTimeOfDay.minute,
                      );
                      if (selectedEndTime.isBefore(selectedStartTime)) {
                        selectedStartTime = selectedEndTime;
                      }
                    });
                  }
                }
              },
              child: Text('Hora do Fim: ${_formatDateTime(selectedEndTime)}'),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                if (titleController.text.isNotEmpty && location.isNotEmpty) {
                  final Color color = getRandomColor();
                  Event newEvent = Event(
                    title: titleController.text,
                    startTime: selectedStartTime,
                    endTime: selectedEndTime,
                    color: color,
                    location: location,
                  );
                  Navigator.pop(context, newEvent);
                } else {}
              },
              child: const Text('Salvar Evento'),
            ),
          ],
        ),
      ),
    );
  }

  String _formatDateTime(DateTime dateTime) {
    return '${dateTime.day.toString().padLeft(2, '0')}-${dateTime.month.toString().padLeft(2, '0')}-${dateTime.year} ${dateTime.hour.toString().padLeft(2, '0')}:${dateTime.minute.toString().padLeft(2, '0')}';
  }
}

class EditEventScreen extends StatefulWidget {
  final Event event;

  const EditEventScreen({Key? key, required this.event}) : super(key: key);

  @override
  _EditEventScreenState createState() => _EditEventScreenState();
}

class _EditEventScreenState extends State<EditEventScreen> {
  late TextEditingController titleController;
  late DateTime selectedStartTime;
  late DateTime selectedEndTime;
  late String location;

  @override
  void initState() {
    super.initState();
    titleController = TextEditingController(text: widget.event.title);
    selectedStartTime = widget.event.startTime;
    selectedEndTime = widget.event.endTime;
    location = widget.event.location;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Editar Evento'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              controller: titleController,
              decoration: const InputDecoration(labelText: 'Título do Evento'),
            ),
            const SizedBox(height: 20),
            TextField(
              onChanged: (value) {
                setState(() {
                  location = value;
                });
              },
              controller: TextEditingController.fromValue(
                TextEditingValue(
                  text: location,
                  selection: TextSelection.collapsed(offset: location.length),
                ),
              ),
              decoration: const InputDecoration(labelText: 'Localização do Evento'),
            ),
            const SizedBox(height: 20),
            TextButton(
              onPressed: () async {
                final DateTime? pickedStartTime = await showDatePicker(
                  context: context,
                  initialDate: selectedStartTime,
                  firstDate: DateTime(2023),
                  lastDate: DateTime(2101),
                );
                if (pickedStartTime != null) {
                  final TimeOfDay? pickedStartTimeOfDay = await showTimePicker(
                    context: context,
                    initialTime: TimeOfDay.fromDateTime(selectedStartTime),
                  );
                  if (pickedStartTimeOfDay != null) {
                    setState(() {
                      selectedStartTime = DateTime(
                        pickedStartTime.year,
                        pickedStartTime.month,
                        pickedStartTime.day,
                        pickedStartTimeOfDay.hour,
                        pickedStartTimeOfDay.minute,
                      );
                      if (selectedStartTime.isAfter(selectedEndTime)) {
                        selectedEndTime = selectedStartTime;
                      }
                    });
                  }
                }
              },
              child: Text('Hora de Início: ${_formatDateTime(selectedStartTime)}'),
            ),
            const SizedBox(height: 10),
            TextButton(
              onPressed: () async {
                final DateTime? pickedEndTime = await showDatePicker(
                  context: context,
                  initialDate: selectedEndTime,
                  firstDate: DateTime(2023),
                  lastDate: DateTime(2101),
                );
                if (pickedEndTime != null) {
                  final TimeOfDay? pickedEndTimeOfDay = await showTimePicker(
                    context: context,
                    initialTime: TimeOfDay.fromDateTime(selectedEndTime),
                  );
                  if (pickedEndTimeOfDay != null) {
                    setState(() {
                      selectedEndTime = DateTime(
                        pickedEndTime.year,
                        pickedEndTime.month,
                        pickedEndTime.day,
                        pickedEndTimeOfDay.hour,
                        pickedEndTimeOfDay.minute,
                      );
                      if (selectedEndTime.isBefore(selectedStartTime)) {
                        selectedStartTime = selectedEndTime;
                      }
                    });
                  }
                }
              },
              child: Text('Hora do Fim: ${_formatDateTime(selectedEndTime)}'),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                if (titleController.text.isNotEmpty && location.isNotEmpty) {
                  Event updatedEvent = Event(
                    title: titleController.text,
                    startTime: selectedStartTime,
                    endTime: selectedEndTime,
                    color: widget.event.color,
                    location: location,
                  );
                  Navigator.pop(context, updatedEvent);
                } 
                else {}
              },
              child: const Text('Salvar Alterações'),
            ),
          ],
        ),
      ),
    );
  }

  String _formatDateTime(DateTime dateTime) {
    return '${dateTime.day.toString().padLeft(2, '0')}-${dateTime.month.toString().padLeft(2, '0')}-${dateTime.year} ${dateTime.hour.toString().padLeft(2, '0')}:${dateTime.minute.toString().padLeft(2, '0')}';
  }
}
