
# Noise reduction in python using spectral gating

The most recent version of noisereduce comprises two algorithms:
1. **Stationary Noise Reduction**: Keeps the estimated noise threshold at the same level across the whole signal
2. **Non-stationary Noise Reduction**: Continuously updates the estimated noise threshold over time

# Stationary Noise Reduction
- The basic intuition is that statistics are calculated on  each frequency channel to determine a noise gate. Then the gate is applied to the signal.
- This algorithm is based (but not completely reproducing) on the one [outlined by Audacity](https://wiki.audacityteam.org/wiki/How_Audacity_Noise_Reduction_Works) for the **noise reduction effect** 
- The algorithm takes two inputs: 
    1. A *noise* clip containing prototypical noise of clip (optional)
    2. A *signal* clip containing the signal and the noise intended to be removed

# Demo 

## Stationary Noise Reduction

![download (1)](https://user-images.githubusercontent.com/33378412/225215165-1d07ea66-217e-49e9-999d-e7ad1c365eeb.png)

https://user-images.githubusercontent.com/33378412/225215196-1582574a-4a2b-4e84-9e74-1421abac6b2a.mp4

## Stationary Remove Noise 

![download (7)](https://user-images.githubusercontent.com/33378412/225215590-4069fe2a-30c7-45dc-9790-c959623f9129.png)

https://user-images.githubusercontent.com/33378412/225215657-e7bcf540-aba2-4866-8436-0f6d935f330c.mp4


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

# Demo 

## Non-stationary Noise Reduction
![download (2)](https://user-images.githubusercontent.com/33378412/225215380-ed49ad47-c62e-42fc-ad3a-a47bd4635cef.png)

![download (3)](https://user-images.githubusercontent.com/33378412/225215399-a6974489-3e1f-4a07-b13b-ee328756f63e.png)

https://user-images.githubusercontent.com/33378412/225215358-9c0f84fc-8d7f-432d-83d7-244a4c5f6c0e.mp4

## Non-Stationary Remove Noise 


![download (8)](https://user-images.githubusercontent.com/33378412/225215842-91987da4-d75e-4d1e-bf13-a368e8c7fe3f.png)

https://user-images.githubusercontent.com/33378412/225215811-dd8135cd-3169-4fc4-9fe6-437ecdce9097.mp4


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
