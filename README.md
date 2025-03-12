# Smart Alarm Clock

This repository contains two Python scripts that together create a customizable “smart” alarm clock system. The system consists of:

1. **alarmclocksettings.py** – A graphical user interface (GUI) for configuring alarms, tasks, triggers, and storing these settings in a local SQLite database.  
2. **smartalarmclocksoftware.py** – A background service/script that interacts with an Arduino (via serial communication), controls Kasa smart devices, checks for scheduled tasks/alarms, and executes them accordingly.

---

## Features

1. **Alarm Time**: Set a specific time to trigger the main alarm.  
2. **Task Scheduling**: Create tasks with triggers that occur on a specific date and time.  
3. **Supports opening websites, launching applications, and initiating WhatsApp-based “call reminders.”**  
4. **Environmental Triggers (Temperature & Humidity)**:
   - Connects to Kasa SmartPlugs to automate devices (e.g., fans or humidifiers) based on thresholds you set for temperature and humidity.  
5. **SQLite Database**: All tasks, alarm times, and device triggers are stored locally (`alarminfo.db`).  
6. **Tkinter GUI**: Friendly interface for easily adding, removing, and viewing tasks.  
7. **Serial Communication with Arduino**: Constantly sends real-time data and instructions to the Arduino and receives sensor data (temperature/humidity) back.

---

## Repository Contents

- **alarmclocksettings.py**  
  - Written in Python with **tkinter** for the GUI.  
  - Allows you to set alarms, add or remove tasks, choose trigger types, and configure environmental threshold values.  
  - Inserts and retrieves data from the SQLite database (`alarminfo.db`).  
  - Discovers local Kasa devices (SmartPlugs, etc.) and shows them in the interface.

- **smartalarmclocksoftware.py**  
  - A Python script running continuously to handle:  
    - Reading from the Arduino over serial (temperature & humidity data).  
    - Turning Kasa SmartPlugs on or off based on temperature/humidity thresholds.  
    - Checking the current time against scheduled tasks.  
    - Opening websites, launching apps, or sending WhatsApp messages when tasks are due.  
  - Uses threads to handle real-time sending of data (e.g., current time) and reading sensor data from the Arduino simultaneously.

---

## Requirements

- **Python 3.7+**  
- **Pip** (Python package manager)  
- **SQLite 3** (typically included with Python/standard libraries)  
- **Installed Python Packages**:
  - `tkinter` (often included with standard Python installations on most platforms)  
  - `tkcalendar` (for calendar/DateEntry widget)  
  - `numpy`  
  - `pywhatkit`  
  - `pyautogui`  
  - `keyboard` (imported as `import keyboard as k`)  
  - `webbrowser` (standard library)  
  - `python-kasa` (for Kasa smart device control)  
  - `pyserial` (for serial communication)  
- **Arduino** with attached sensors for temperature/humidity (optional but required if you want to read real data).  
- **Kasa SmartPlugs** or other Kasa devices (optional but required if you want to control power devices).

---

## Usage

### Set Up the Database
The scripts will automatically create and/or update the `alarminfo.db` database tables on startup. No manual creation needed.

### Configure the COM Port
In **smartalarmclocksoftware.py**, look for the line:
python Copy Edit arduinodata = serial.Serial('com14', 9600)

Replace `'com14'` with the correct serial port for your Arduino on your system (e.g. `'/dev/ttyACM0'` for Linux, `'/dev/ttyUSB0'`, or `COM3` on Windows).

### Run `alarmclocksettings.py`
This opens a GUI window.

- **Alarm Time**: Enter a time in `HH:MM` format in the top-left field and click **Add alarm time** to store it in the DB.
- **Add Task**:
  1. Enter a name/description of your task.
  2. Select the trigger type (website, app, or WhatsApp call) from the dropdown.
  3. Enter the detailed trigger info (e.g., URL, path to EXE, or phone number).
  4. Choose the date from the calendar widget and time in `HH:MM` format.
  5. Click **Add task** to save it.
- **Set Environmental Triggers**:
  1. Enter high/low temperature thresholds (e.g., `30,20`) in the corresponding field.
  2. Enter high/low humidity thresholds (e.g., `60,40`) in the corresponding field.
  3. Choose which discovered Kasa devices will handle temperature or humidity triggers.
  4. Click **Add triggers**.

### Run `smartalarmclocksoftware.py`
This script runs in the background. It will:

- Constantly read sensor data from the Arduino and switch Kasa devices on/off when temperature or humidity goes above or below your defined bounds.
- Check the current time; if it matches a scheduled alarm or task, it sends signals to the Arduino and executes the associated action (open website, app, or WhatsApp call).

### Completing Tasks
In the GUI, if you have tasks listed, you can select one and press **Complete Task** to remove it from the schedule.

---

## Recommended Workflow

1. Launch `alarmclocksettings.py` first to configure all your tasks, triggers, and alarm time.  
2. Close the GUI once finished, or leave it open.  
3. Run `smartalarmclocksoftware.py`. The software will monitor the Arduino data, control smart plugs, and trigger tasks at the correct times.

---

## Notes and Troubleshooting

- **tkcalendar**: Make sure you have `tkcalendar` properly installed or you may get import errors.
- **Serial Port**: If you run into issues with serial port access, ensure you have the correct permissions (e.g., on Linux you might need to add your user to the `dialout` group).
- **Kasa Device Discovery**: If your devices aren’t discovered, check that your computer and Kasa devices are on the same local network and that the Kasa devices are powered on.
- **WhatsApp Automation**: `pywhatkit` opens WhatsApp Web in the browser. You may need to be logged into WhatsApp Web in advance. Also, the script sets a small delay (e.g., 2 minutes from the current time) to properly send messages/calls.

---

## Contributing

Feel free to open issues or pull requests. Contributions that improve stability, add features, or enhance documentation are welcome.
