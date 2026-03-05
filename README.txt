Radioberry YouTube Live Streamer
This project contains the configuration for streaming the Radioberry screen output and system audio to YouTube Live. It uses ffmpeg with the h264_omx codec for hardware-accelerated video encoding, ensuring smooth performance on Raspberry Pi hardware.
Prerequisites
•	FFmpeg: Must be installed with libpulse and omx support.
•	X11 Server: The script grabs the display from the X11 window system.
•	PulseAudio: Used to capture the monitor output of your audio device.
•	YouTube Stream Key: You will need your unique API key from the YouTube Creator Studio.
The Command
Run the following command in your terminal to start the stream. Replace YOUR_API_KEY with your actual YouTube stream key.
Bash
ffmpeg -f x11grab \
    -thread_queue_size 256 \
    -video_size 950x400 \
    -framerate 8 \
    -i :0.0 \
    -f pulse \
    -thread_queue_size 512 \
    -i alsa_output.usb-C-Media_Electronics_Inc._USB_Audio_Device-00.analog-stereo.monitor \
    -c:v h264_omx \
    -b:v 400k \
    -r 8 \
    -c:a aac \
    -b:a 48k \
    -ar 44100 \
    -ac 1 \
    -f flv "rtmp://a.rtmp.youtube.com/live2/YOUR_API_KEY"
Parameter Breakdown
Input Settings
•	-f x11grab: Captures the X11 desktop display.
•	-video_size 950x400: Defines the specific resolution/window size of the Radioberry interface.
•	-i :0.0: The display number (default desktop).
•	-f pulse -i [device_name]: Captures audio from the specific USB Audio Device monitor (stereo mix).
•	-thread_queue_size: Increases the buffer size to prevent "Thread message queue blocking" warnings.
Video Encoding
•	-c:v h264_omx: Uses the OpenMAX (OMX) hardware encoder for H.264, significantly reducing CPU usage on Raspberry Pi.
•	-b:v 400k: Sets the video bitrate to 400kbps (suitable for static or low-motion radio screens).
•	-r 8: Sets the output framerate to 8 FPS to match the input.
Audio Encoding
•	-c:a aac: Uses the AAC codec for compatibility with YouTube.
•	-b:a 48k: Sets audio bitrate to 48kbps.
•	-ar 44100: Sets the audio sampling rate to 44.1kHz.
•	-ac 1: Converts audio to mono (1 channel).
Output
•	-f flv: Sets the output format to FLV (required for RTMP streaming).
•	rtmp://a.rtmp.youtube.com/live2/...: The destination YouTube RTMP ingest server.
Troubleshooting
1.	Audio Device Not Found: If the audio fails, list your available pulse sources using: pactl list sources short Ensure the name matches the -i parameter in the audio section.
2.	Permissions: Ensure the user running the command has permissions to access the X server (usually requires being logged into the desktop session).
3.	Network: If the stream drops, check your upload bandwidth; 400-500kbps of steady upload is required.

