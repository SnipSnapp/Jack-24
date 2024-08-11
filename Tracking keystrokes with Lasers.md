
95' this guy was 10 85'. 
mIRC = what he got onto.
They found winnuke me 95 to crash folks computers, but sp2 resolved this. That's when he found phrack in for win 96'.  Read smashing the stack for fun and profit.  If you type over the 100 bytes you can overwrite anything. and do stack overflow. 
IT's only when you understand CPU? architecture, assembling, and linking do you find how you can attack stuff.  It all boils down to energy. 
energy + time = power. 
and h

So if you have a usb 2.0,  energy moving over something and its information.  There's information, the 5v moves up and down a little bit when data travels.  Data is just stuff traveling through the data lines.  So you can always extract this stuff. 
So he got a text message, and the speakers could panic and start making weird noises. so there's interference.  So if your phone can do that, if he looks at how a keyboard works, can a phone interview with a keyboard. 
Every conductor is a unique copper length.  Every piece of metal is an antennae.
measurement of each thing is a wavelength.  So can you make an EM keyboard to inject keystrokes.  But he started to learn about physics and light. 
So EM waves are really light and radio waves and whatnot.
You split the light up into tis wavelengths, but radio is light and colors of it we can't see with our eyes. 
SO thinking physics, this guy learned how to bust CRTs.
So he found someone who would teach them how to blow glass.
He looks in the email and he finds their phone number, she was down the street and go into ian's glass blowing station. 

Ian's shop shows the stuff.
They showed him how to blow glass the right way.  He started to then learn about waves and physics. 
It's difficult for him to understand complex thing. 
If he can find patterns his life is easier. 
A wave has 3 things.  An amplitude, wavelength, and a phase. 
They have reflection refraction/defraction
Amps = intensity (brightness)
wavelength = (red v blue) (bass vs. high pitch)
phase = where something is in its amplitude.
sample rate or frame rate is the same same.  but bit rate is depth/quality/resolution

Waves can do things like interference. So we can do constructive and destructive interference.  

Destructive interference can help. 
Some cool attacks we can do with light. 
If you have sound playing out aof a speaker, you can do the opposite. 

If you have a speaker, and air moving that speaker cone, you can turn a mic into a speaker and the reverse.  This is true of the reverse.  You can convert and read light from led's too.


So to take advantage of energya nd energy conversion, we can see the different colors.  UV/IR is radio and light, diff colors we can't see.  FM radio is frequency modulation.  So when there's audio, a 10kHZ tone, 


Light commands research, found if they shine a laser at google home, you can say hay google to it.  You just turn it on and off to the frequency of sound.  That makes heat, and that is vibration of particles.  You can shine light through a window and get it to do things. 
Infrared lights, pressing the keys on something too we can see how hot things get and do something similar. 

NSA found in the 60's a cathode ray tube would emite an RF, you can hear this in an adjacent room and see this on the monitor...
which is a legit attack. 

There's one and it uses a mag stripe.....
so you can take this, get a magstripe and convert the chip to a non chip card. 
So sound. 
Audible is 20-

You can use a microphone to listen to the osund of a keyboard . 
They can take a mic, get the sound a computer is making and convert that to keys.
You can use a mic from a std cell and listen to the ultrasound from a computer.  You have to get the mag from all the things. so these things move around and you can pull them out the PC. 
You can convert it back to 1's and 0's to get keys. Just listening to ultrasound from a device.

So laser microphone = light + sound. 
The first microphone that was passive was the theremin. This intstrument. Same guy made something called "The thing" which he gave to the kgb. it's a passive card which goes into an rfid field which makes radio, rfid. 
- This was first implementation of rfid.
- So he made this into a microphone, which he turned into a seal.  They put this in, with no battery. 
- It took forever for folks to realize it was an rfid tag made into a mic.

If you can sign a light at a window, you can have sound go through it.  Sound goes through a lot, you don't need a laser, but some light you can receive.

Sniffing lasers with and keystrokes/voltmeters.
These people pointed a red laser on the reflective part of a laptop, and could plug it in, so they could listen to the keystrokes someone is making with a laser beam. 

Keysweeper is an arduiino device in an USB charger, which sniffs all the wireless keyboards nearby. It can inject keystrokes and listen to all keyboards in the industry. 

Keystrokes, demonstrated that you use common stuff, they had a laser, hit a laptop, reflect off of it, then they had a photo diode/resistor,  The converts the light waves to electricity.  So all of this is traveling at a frequency, so you can take advantage of that. Connecting a transformer to an AC line, you can measure whenever it crosses 0.  Is it actually 60x/sec or what? So if clock on phone is not on wifi, you can use 60Hz from wall to get things so accurate. 
- It's only accurate fora. lil while, then drifts. 
- So you can say "If I measure the drift over time, you can measure it as a fingerprint of a location. "
- You can see the drift, and take that and measure it for everything. 
Laser microphone improvements. 

Lasers aren't fun, but can burn your eyes. use laser safety goggles. 5 milliwat doesn't mean only 5 milliwatt.

hardware
- tried first to implement original laser mic, and took a while to get working, but sound quality wasn't great. When you can hear it, feelz good man. 
- normally you have a laser and you point it at a window,  
	- there is so much noise, 60Hz from wall, all of the low frequency stuff that messes with your signal. 
	- So with a hardware you use an rf bias-tee to power the laser, and oscillate things to get away from harming things.  
	- If you turn light on and off at radio frequencies, you can get away from a lot of noise. 
- Collumated beam is a beam tht doesn't focus or defocus. A laser always has the same pase of light coming through it. 
- Shudder to tighten the beam. 
- Shutter helps close the beam, 
- With a laser didoe can use a red light, and move it to infrared. 
- so on rx side.  You have a photodiod e and a sound card.  So you start modulating this thing at mhundreds of thousands of times per second.   ut you want to get away from low frequencies.  Because these are disruptive. 
- Not much light coming off of it.  So the soundcard can't look at everything we want.  So we need something that's not a soundcard that can accept it.  
- Can he use gnu open radio? No, because then you need an SDR to pull the data in.  He needs to go double that sample rate.  But SDRs never go as low as he needs.  But the minimum frequency is still not low enough. 

USRPb210 has 12 bits, but when you need 70 MHz, you need more.  But when you have 125 MHz/signal you need more stuffs. 
Amplifier goes into a ham radio,  
When lasers point, to a computer,  it gets hard.  
Best he could get was 4-5 khz of sounds. if he got too high or too low couldn't do it. 

So with an infrared laser diode, he got an infrared camera. he was showing the laser diode hitting a white square.  So he can see, he made another recording at the laptop. 
But he also saw somethin interesting, and when he unlocked his phone, he saw all thse dots, on the IR recording.  When he pulled out his phone, it's LiDAR on the back.  It only happens when the camera app is open, it's using it to figure out how far an object is. to decide which camera to use. 
So now, you have a little device, witha. photo sensor, can measure for when someone points a phone camera at you before they're recording. 

So RF modulation. 
- getting into the RF domain.  The photo diode, spot 2D.  this one has 2 cells.  Biggest challenge is you need to get to the edge of the diode.  You want your reflection to go on and off that diode. Edge is where you need to go and get to. 
- Software you use.  
- GNU radio. 
Things circled in blue are graphical sinks for information. 
Sample rate of 4.8 MHz/sec.  120 MHz. Then 200KHz. Don't want to listen to some things as all SDRs have a DC spike you have to filter out for.  Can also use a logivc analyzer.  Then a translating FIR filter, which is something that changes the frequency you're at.  You want to center on the sound.  Sound is moving up and down. But our AM modulated signal is going up and down.  So we're saying we only 