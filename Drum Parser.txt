"""
Midi to csv from https://github.com/andrewchenk/midi-csv/tree/master
Modified to read drums
"""



import os
import random
import sys
import argparse

import music21
import pandas as pd


def is_dir(dirname):
    """Checks if a path is an actual directory"""
    if not os.path.isdir(dirname):
        msg = "{0} is not a directory".format(dirname)
        raise argparse.ArgumentTypeError(msg)
    else:
        return dirname


def main():

	input_dir = "/data/Drum Patterns"
	output_dir_name = "/data/-output-csvs-3/"

	if not output_dir_name:
		output_dir_name = input_dir + "-output-csvs"

	if not os.path.exists(output_dir_name):
		os.makedirs(output_dir_name)

	print("Outputting csv files in to " + output_dir_name)
	df = pd.DataFrame(columns=["file_name", "instrument", "start_time", "duration", "velocity", "tempo"])
	for file in os.listdir(input_dir):
		filename = os.fsdecode(file)

		mf = music21.midi.MidiFile()
		mf.open(input_dir + "/" + filename)
		mf.read()
		mf.close()
		s = music21.midi.translate.midiFileToStream(mf, quantizePost=True).flatten() #quantize is what rounds all note durations to real music note types. In this case it's set to True since we want the notes neatly mapped in a pattern
    
		df = pd.DataFrame(columns=["file_name", "instrument", "start_time", "duration", "velocity", "tempo"])
		for g in s.recurse().notes:
				x = music21.note.Note(None, duration=g.duration)
				x.instrument = g.getInstrument()
				x.volume.velocity = g.volume.velocity

		# ALERT: assumes only one tempo throughout the file
		note_tempo = s.metronomeMarkBoundaries()[0][2]
		for note in s.recurse().notes:
			new_df = pd.DataFrame([[filename, note.getInstrument(), round(float(note.offset), 3), round(note.duration.quarterLength, 3), note.volume.velocity, note_tempo]], columns=["table_name", "instrument", "start_time", "duration", "velocity", "tempo"])

			df = pd.concat([df, new_df], ignore_index=True)

		df.to_csv(output_dir_name + "/" + filename[:-4] + ".csv")

	print("Done creating csvs!")


if __name__ == "__main__":
	main()