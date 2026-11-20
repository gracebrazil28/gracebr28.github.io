---
title: "OH masers in W3(OH), from raw telescope voltages"
entry: 1
summary: "Eight minutes of public Allen Telescope Array data, processed from raw samples to a maser spectrum, its polarization, and a distance that comes out twice as far as it should."
target: "W3(OH), 1665 MHz hydroxyl line, Allen Telescope Array"
log:
  - label: Target
    value: "W3(OH), a compact HII region in the W3 star-forming complex, Perseus Arm"
  - label: Line
    value: "OH ground state, 1665.4018 MHz (18 cm)"
  - label: Telescope
    value: "Allen Telescope Array, one 6.2 m antenna, two linear feeds (X and Y)"
  - label: Data
    value: "Public raw IQ recording by Daniel Estévez, 1.92 Msps, 3.9 GB per feed"
  - label: Recorded
    value: "2022-06-21, 19:36 UTC, 506 seconds"
  - label: Result
    value: "Maser emission at −46.5 to −44 km/s, peak 16σ; one feature nearly fully polarized"
downloads:
  - label: "Ongoing Computational Analysis of Hydroxyl (OH) maser W3(OH), PDF"
    url: /assets/pdf/Hydroxyl__OH__maser_W3_OH__Analysis__Nov20.pdf
    note: "November 20, 22 pages: the current write-up"
  - label: "Initial Computational Analysis of Hydroxyl (OH) maser W3(OH), PDF"
    url: /assets/pdf/Hydroxyl__OH__maser_W3_OH__Analysis__Nov_13.pdf
    note: "November 13, 10 pages: the first pass"
  - label: "Code and README"
    url: /projects/w3oh/
    note: "oh_spectrum.py and the synthetic test generator"
  - label: "The dataset on Zenodo"
    url: https://zenodo.org/records/6711078
---

I've been building toward hydrogen-line observations with my own SDR, and I wanted to try post processing on data where I knew the answer existed. Daniel Estévez published exactly a raw voltage recording of the hydroxyl (OH) line toward W3(OH), made with one dish of the Allen Telescope Array. It isn't a spectrum. It's eight minutes of raw samples from both feeds.

I worked through it in two rounds, a week apart, and wrote each one up. Both write-ups are linked at the bottom.

## Why this source

W3(OH) is one of the best-studied OH maser sources in the sky. Masers are the microwave cousin of lasers: in the dense gas around a young massive star, OH molecules amplify the 1665 MHz line. So instead of a broad, smooth hump like hydrogen you get a cluster of very bright, very narrow spikes. Each spike is a small clump of gas moving at its own speed along the line of sight. OH also responds strongly to magnetic fields, which makes these masers one of the few direct ways to measure fields in star-forming regions.

## First look: nothing

I opened the raw file in inspectrum, a spectrogram viewer, expecting to see something. It was noise from edge to edge. That turned out to be the first lesson: each column of the display is a single FFT, and the maser adds only a few percent to the receiver's own noise. You can't see it without averaging.

## Averaging 506 seconds

Averaging about 59,000 spectra per feed brought it out. After dividing out the receiver's bandpass and converting frequency to velocity, corrected for Earth's motion (+14.9 km/s at the time of the recording), the emission sits between −46.5 and −44 km/s, peaking at −45.2 km/s at about 16σ.

<figure>
  <img src="{{ '/assets/images/w3oh/xy_rows.png' | relative_url }}" alt="Four-panel plot: the raw bandpass of both feeds, the baseline-removed maser profile peaking near minus 45 kilometres per second, a waterfall with a steady vertical maser stripe, and a gain-versus-time plot where the X feed jumps by up to 6 percent.">
  <figcaption>From top: the raw bandpass of both feeds (the maser is the small bump at +335 kHz); the line profile; the waterfall over the full 506 s; and how much each feed's gain changed during the recording.</figcaption>
</figure>

The noise check was the moment I trusted it. The radiometer equation predicts how much noise should remain after averaging 117 Hz channels for 506 seconds across two feeds: 0.0029 of the system noise. I measured 0.0027. The data is limited by thermal noise, not by leftover baseline wiggles or interference.

## The X feed misbehaved

My first waterfall had bright horizontal bands across every channel. They weren't astronomical: the X feed's gain jumped by up to 6% partway through, while the Y feed drifted by less than 1%. A change in the sky or the pointing would hit both feeds, so this was almost certainly the X electronics. Rescaling each 10-second row to its own gain removed the bands, and the maser turned out to be steady for the whole recording (bottom two panels above).

## Five components

Fitting the profile as a sum of Gaussians, with the number of components chosen by a statistical test (the BIC) rather than by eye, gave five:

| Velocity (km/s) | Width (km/s) | Note |
|---|---|---|
| −46.22 | 0.31 | narrow |
| −45.20 | 0.48 | main peak |
| −45.30 | 1.67 | broad, weak; about a third of the line area |
| −44.13 | 0.35 | weak |
| −42.68 | 0.15 | tentative, about 3.7σ |

<figure>
  <img src="{{ '/assets/images/w3oh/xy_rows_fit.png' | relative_url }}" alt="The maser profile with five fitted Gaussian components and residuals that stay within plus or minus 2 sigma.">
  <figcaption>The five components and their sum. The residuals (bottom) stay within the noise.</figcaption>
</figure>

The widths matter. Gas emitting thermally can't produce a line narrower than its temperature allows: for OH at 50 K that's 0.37 km/s. Three of these components are narrower, which would need gas colder than about 36 K, 44 K and 8 K. That's the fingerprint of maser amplification, which narrows the line. The main peak is wider than the individual maser spots that interferometers resolve in W3(OH), which fits a blend of several spots inside the antenna's two-degree beam.

## Polarization

Because both feeds share one local oscillator, they can be cross-correlated, which gives the full set of Stokes parameters, the standard description of polarization.

<figure>
  <img src="{{ '/assets/images/w3oh/w3oh_stokes.png' | relative_url }}" alt="Three-panel polarization plot: total intensity and linear polarization, the cross-correlation between the feeds, and the phase between feeds across the line.">
  <figcaption>Top: total intensity (I) and the X-minus-Y difference (Q). Middle: the part of the signal correlated between the feeds. Bottom: the phase between the feeds, which sweeps across the main peak.</figcaption>
</figure>

- **The −46.2 km/s feature is essentially fully polarized** (115 ± 8%, within 2σ of 100%), yet equally strong in both feeds. A strongly circularly polarized maser would look like that.
- **The main feature is clearly stronger in Y** (q = −0.18 ± 0.02, 7.7σ). Because the −46.2 km/s feature shows no such difference, it isn't just one feed being noisier.
- **The phase between the feeds sweeps across the main peak**, from about 180° on its blue side to about 0° on its red side. The main peak is a blend of components with different polarization.

<div class="note-pencil">
<strong>Not proven:</strong> the red side of the main peak sits 180° from the −46.2 km/s feature, which means opposite polarization. If both are circular, they could be a Zeeman pair: one maser split in two by a magnetic field of roughly 2 milligauss. But the phase between the feeds was never calibrated, so circular and linear polarization look the same here, and a single dish can't show the two features come from the same clump.
</div>

## A distance that's twice too far

Galactic rotation links a cloud's velocity to its distance. Plugging in −45.2 km/s gives a kinematic distance of 3.8 kpc. But W3(OH)'s distance has been measured directly, by maser parallax, at 1.95 kpc (Xu et al. 2006). At that distance, simple rotation predicts −26 km/s.

<figure>
  <img src="{{ '/assets/images/w3oh/xy_rows_fit_kinematic.png' | relative_url }}" alt="Velocity expected from Galactic rotation versus distance, crossing the measured velocity at 3.8 kiloparsecs, while the parallax distance of 1.95 kiloparsecs corresponds to minus 26 kilometres per second.">
  <figcaption>Circular rotation (blue) crosses the measured velocity at 3.8 kpc. At the parallax distance, it predicts a velocity 19 km/s off.</figcaption>
</figure>

The 19 km/s gap isn't an error in my spectrum. I've read in academic paper that it is the non-circular motion in this part of the Perseus Arm, and the parallax measurement that revealed it is a well-known result. Getting there from raw voltages was very satisfying.

## Checking my own work

To make sure the pipeline wasn't inventing results, I generated a synthetic recording with known answers: a 3% gain jump in one feed, a phase offset between feeds, and five lines in five different polarization states. The pipeline recovered all of them, within about 2σ.

<figure>
  <img src="{{ '/assets/images/w3oh/synthetic_stokes.png' | relative_url }}" alt="Polarization analysis of the synthetic test recording, recovering five lines with known polarization states.">
  <figcaption>The synthetic test. One line is polarized along X (seen only in Q), three are correlated between the feeds, one is unpolarized, and the two circular lines come out 181° apart, as they should.</figcaption>
</figure>

It also confirmed something about the real data: the synthetic feeds share almost no correlated noise (0.01%), while the real ones share 5%. That correlation comes from the telescope, not from my code.

## Summary of my two experiments and write-ups

**November 13, the first pass.** I got from raw samples to a calibrated spectrum: averaging, removing the bandpass, correcting the velocity for Earth's motion, a first waterfall and a kinematic distance. Most conclusions were read off the plots by eye. I noticed the main peak looked stronger in Y than in X and called it a possible sign of polarization.

**November 20, the second pass.** I went back to turn impressions into measurements:

- rescaled each row of the waterfall to its own gain, which removed the bands and exposed the X-feed jumps;
- checked the noise against the radiometer equation instead of estimating it by eye;
- fitted the profile with Gaussian components instead of reading off peaks, which turned the "shoulder" into a broad component and turned up a tentative fifth feature;
- cross-correlated the two feeds to measure polarization properly, instead of comparing the two profiles by eye;
- built the synthetic test recording to check that the pipeline gets known answers right

The first write-up is worth keeping next to the second: comparing them shows which early impressions remained plausible (the Y-stronger main peak, the distance discrepancy) and which didn't (the shoulder).

## Mistakes worth recording

- **Velocity frames.** In one early run I set the plot window in one velocity frame while the data was in another, and cut the peak in half without noticing. The 15 km/s correction for Earth's motion is easy to forget.
- **Reading plots by eye.** I first described a "shoulder" near −44.9 km/s. The fit showed it's better explained as a broad, weak component under the main peak.
- **Environment trouble.** A half-upgraded SciPy broke imports partway through. A fresh conda environment fixed it.

## Next

- Split the recording in half and check that every feature appears in both halves.
- Look for a way to calibrate the phase between the feeds, to test the Zeeman idea properly.