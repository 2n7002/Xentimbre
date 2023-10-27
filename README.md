# Xentimbre
### A webapp for creating custom timbres
Partially inspired by Chromaphone 3's timbre customization features, Xentimbre allows for detailed timbre customization with up to 32 harmonics, defining their ratios, amplitudes, and phase offset. These timbres can be played back in the browser, with a graph of the dissonance curve and generated sound wave, and the timbre can be exported as a .wav file of specified duration.

#### How to use
As of now, Xentimbre is not hosted on a site, so it has to be downloaded as a .html file and opened in the browser manually. None of the icons (including the app icon I still need to make) will be loaded with this file, nor will the font be loaded, but the app still has full functionality this way. 

The player uses the A key as the base pitch, which is C4 = 262 Hz by default. Each key is 1 equal division step apart, with 12ed2 tuning by default. The reference frequency and tuning can be modified to the left of the harmonics in the center, along with the page volume, which can be adjusted with the slider or the + - keys.

To make a timbre, enter a valid decimal or fraction greater than/equal to 1:1 into each "Ratio:" textbox, and enter an amplitude from 0-1 into each corresponding "Amplitude:" textbox. Clearing the ratio OR amplitude will remove the harmonic from playback. Phase offsets are optional, and can be between -0.5 and 0.5. The dissonance curve and sound wave graphs will be updated each edit, and then the timbre can be used with the keyboard player. 

Because amplitudes quickly add up to be quite large, especially in the preset timbres which use all 32 harmonics, both the playback and file export use a normalized amplitude based on the average of all of the harmonic amplitudes. Also, for the player, any frequency above 20,000 is excluded due to the limitations of both human hearing and the web audio API.

The attack envelope can be modified to the right of the harmonics in the center, by dragging the ADSR sliders.

While the page is selected, the player will listen for keyboard inputs unless a textbox is selected. Keys Z to . are pitches 1-9, keys A-L are pitches 10-18, and so on. If a key is stuck or if the sound is too loud by accident, pressing the space bar will stop all sound coming from the page. 

#### File exports
When "Export sample" is clicked, if a valid sample duration is specified, a download will be generated for a .wav containing the timbre. The timbre will play at the reference frequency set in the player. The exported sound is normalized to the average of the amplitudes of each harmonic, but the file is then additionally adjusted to ~75% volume regardless of what the player volume is.

#### How the dissonance curve is calculated

The function I used for the dissonance curve is a modification of Sethares' parameterization, which instead only takes one frequency *ratio* and uses a fixed reference frequency of 400 Hz. Additionally, Sethares' parameterization returned a value from 0 to ~0.1808, so I scaled it to 0 to 1. Note that his formula, as well as mine, will not work for fractions less than 1. This is part of the reason why the user-defined intervals in each harmonic have to be greater than or equal to 1. Also, phase offsets do not change the calculated dissonance, as I do not know how to account for this or if it even makes a difference.
```js
function disscalc(r) {
  // This is the inverse of the exact maximum value Sethares' original parameterization returns, ~0.1808
  return (Math.pow(23, 23 / 9) / (9 * Math.pow(14, 14 / 9)))
  // followed by the dissonance calculation itself, where r is the frequency ratio
    * (Math.exp((-1680 / 137) * (r - 1)) - Math.exp((-2760 / 137) * (r - 1)));
}
```
