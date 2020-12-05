# Data formatting for DeepSpeech

DeepSpeech expects audio files to be WAV format, mono-channel, and with a 16kHz sampling rate.

For training, testing, and development, you need to feed DeepSpeech.py CSV files which contain three columns: `wav_filename,wav_filezie,transcript`. The `wav_filesize` (i.e. number of bytes) is used to group together audio of similar lengths for efficient batching.