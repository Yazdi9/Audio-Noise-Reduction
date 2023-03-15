
# Noise reduction in python using spectral gating

The most recent version of noisereduce comprises two algorithms:
1. **Stationary Noise Reduction**: Keeps the estimated noise threshold at the same level across the whole signal
2. **Non-stationary Noise Reduction**: Continuously updates the estimated noise threshold over time

# Stationary Noise Reduction
- The basic intuition is that statistics are calculated on  each frequency channel to determine a noise gate. Then the gate is applied to the signal.
- This algorithm is based (but not completely reproducing) on the one [outlined by Audacity](https://wiki.audacityteam.org/wiki/How_Audacity_Noise_Reduction_Works) for the **noise reduction effect** ([Link to C++ code](https://github.com/audacity/audacity/blob/master/src/effects/NoiseReduction.cpp))
- The algorithm takes two inputs: 
    1. A *noise* clip containing prototypical noise of clip (optional)
    2. A *signal* clip containing the signal and the noise intended to be removed

### Steps of the Stationary Noise Reduction algorithm
1. A spectrogram is calculated over the noise audio clip
2. Statistics are calculated over spectrogram of the the noise (in frequency)
3. A threshold is calculated based upon the statistics of the noise (and the desired sensitivity of the algorithm) 
4. A spectrogram is calculated over the signal
5. A mask is determined by comparing the signal spectrogram to the threshold
6. The mask is smoothed with a filter over frequency and time
7. The mask is appled to the spectrogram of the signal, and is inverted
*If the noise signal is not provided, the algorithm will treat the signal as the noise clip, which tends to work pretty well*

# Non-stationary Noise Reduction
- The non-stationary noise reduction algorithm is an extension of the stationary noise reduction algorithm, but allowing the noise gate to change over time. 
- When you know the timescale that your signal occurs on (e.g. a bird call can be a few hundred milliseconds), you can set your noise threshold based on the assumption that events occuring on longer timescales are noise. 
- This algorithm was motivated by a recent method in bioacoustics called Per-Channel Energy Normalization. 

### Steps of the Non-stationary Noise Reduction algorithm
1. A spectrogram is calculated over the signal
2. A time-smoothed version of the spectrogram is computed using an IIR filter aplied forward and backward on each frequency channel.
3. A mask is computed based on that time-smoothed spectrogram
4. The mask is smoothed with a filter over frequency and time
5. The mask is appled to the spectrogram of the signal, and is inverted

# Installation
`pip install noisereduce`

# Usage
See example notebook: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/timsainb/noisereduce/blob/master/notebooks/1.0-test-noise-reduction.ipynb)


### Simplest usage
```
from scipy.io import wavfile
import noisereduce as nr
# load data
rate, data = wavfile.read("mywav.wav")
# perform noise reduction
reduced_noise = nr.reduce_noise(y=data, sr=rate)
wavfile.write("mywav_reduced_noise.wav", rate, reduced_noise)
```
