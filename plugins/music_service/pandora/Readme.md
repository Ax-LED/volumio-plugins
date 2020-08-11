# Pandora plugin for Volumio

## Getting Started

### Downloading the Source Code from GitHub

First you'll need to SSH to your Volumio machine.<br/>
To enable SSH access, browse to http://volumio.local/dev and turn it on.<br/>
<br/>
Connect to your Volumio machine.<br/>
Use PuTTY on Windows or some equivalent.<br/>
Mac users can use a terminal window, ask a search engine for help, or visit an Apple store.<br/>
Linux users, you're fine.<br/>
<br/>
<b>Username:</b> `volumio`<br/>
<b>Password:</b> `volumio`<br/>
<br/>
Then, clone the repository:

`git clone https://github.com/truckershitch/volumio-plugins.git`<br/>

### <b>Optional:</b> There are two older versions archived on GitHub:

If you want to try out another branch (at this point I would not bother -- Volumio has the 1.0.0 version already) change to the volumio-plugins directory:

`cd volumio-plugins`

The pianode branch is the oldest and works the least.  I have not tested it on the newer Volumio releases.<br/>
<b>It may break your system.  It probably won't work.</b><br/>
<br/>
To try your luck with the version based on pianode, do this:

`git checkout pianode`

To try out version 1.0.0 that uses the volatile state (works but not perfectly), do this:

`git checkout v1.0.0`

Otherwise, just continue below (don't bother with checking out anything).  To switch back to the main master branch if you checked out another one, do this:

`git checkout master`

Or you can just delete the `volumio-plugins` directory.

## Continuing with Installation

### Upgrade From Older Version

~~Before installing any of these plugins manually, you should uninstall any other version from the Volumio Plugin menu or by deleting the installation directories yourself.~~<br/>

~~To delete the directories manually (be careful to only delete the pandora directories!):~~

~~`rm -rf /data/plugins/music_service/pandora`~~<br/>
~~`rm -rf /data/configuration/music_service/pandora`~~

~~Then execute `volumio vrestart`~~

My apologies on the command-line instructions.  I was just dumping the files into `/data/plugins/music_service/pandora`.  I took another look at this.<br/>

<b>To upgrade from an older plugin version:</b>

`cd /path-to/volumio-plugsin/plugins/music_service/pandora`<br/>
`volumio plugin update`

<b>For a fresh installation:</b>

`cd /path-to/volumio-plugins/plugins/music_service/pandora`<br/>
`volumio plugin install`

Both of these two commands stop for me after 100%.  I'm not sure why; if you look at `install.sh`, it's pretty empty.  Weird.  The operations succeed.<br/>
No worries.  Just hit `Control-C`.<br/>

Go to the Plugins sidebar in Volumio and enable the Pandora plugin.  On the first run, you will have to enter your credentials with the settings button.  You may need to restart the plugin or restart Volumio after this step.<br/>
<br/>
The stations will populate after login.  You can browse to the Pandora icon and then load a station.<br/>
The station list is (currently -- is this needed?) not refreshed until you reload the plugin, so if a new station is added, the index will be wrong.<br/>
<br/>
You should be up and running at this point.<br/>

## Prerequisites

I can't think of any prerequistes other than SSH access to Volumio and a Pandora account.<br/>

## Changes

Much was changed for version 2.x:

* Much cleaner codebase.  I now have a better sense of how Promises really work.  I was sort of winging it before for version 1.0.0.
* Tracks actually load up in the Volumio queue now and you can hop around and pick the ones you want.  The queue management was actually a bit tricky for me to iron out, but it should be working just fine now.
* Undesired bands/artists can be filtered by entering a percent (%) delimited string in the configuration, i.e. Megadeath%Abba%Korn
* No more volatile state.  The 1.0.0 plugin was updating the state every second.  It really was difficult to see what was going on with the constant barrage of state update log messages.
* Track data downloaded from Pandora only works for about an hour.  Track lifetime is now checked in the background and entries are deleted in a sane fashion in case the user does not listen to them in time.
* Dual-function Previous button option.  If enabled, a single press replays the current track, and a quick double-press goes to the previous track (when not in shuffle/random, otherwise a random track is played).
* Version 2.1.0: Actual support for Pandora One high-quality streams!  I took another look at this and I'm pretty sure that Pandora One users will get 192 Kbit/s streams now.  I do not have a premium subscription so if this does not work, please tell me.  It should, though, as the Unofficial Pandora API has a JSON of a sample playlist object on their site.  Free users like me are stuck with 128 Kbit/s.
* Version 2.1.2: Changed version number that npm didn't like (2.1.1.1).  This Readme was amended, mainly to clarify the experimental, mostly non-working, historical status of the pianode branch.  The installation steps were clarified.  A few things were fixed when the plugin closes (removing it from the Volumio Sources, stopping the track expiration loop).
* Version 2.3.0: Optional Thumbs-Down sent to Pandora for a track skipped by the Next media button.  The track is also removed from the queue like the sad thing it is.  Flip the switch in the plugin settings and kick the lame tracks to the curb!

## Issues

* ~~Next track is not working properly.  Hopefully there will be a fix!~~<br/>
Previous and Next buttons now work as expected.  The key was this:<br/>
`self.commandRouter.stateMachine.setConsumeUpdateService = your-service-here`<br/>
After that, the functions defined for previous and next in the plugin worked fine.
* There may be a few bugs left.  I have been working on this for a while but you never know.  I can say that it will play and play if you leave it alone.
* If you run Volumio on a PC in a browser tab, or maybe a window, at least in Firefox, and you decide to pay some bills or write an angry letter to your neighborhood association about kids being on your lawn, the timer for the current track will lag behind.  This corrects itself on the next state update at the end of the track.  I'm not sure if there is an easy fix here, or if the state should be pushed every ten seconds (seems like a hack).  Playback is not affected, everything sounds fine, songs are not cut off.
* It may be possible to add a station with the Volumio search function.  I am looking into it.  The functionaliy is there.
* Volumio has a consume mode.  As far as I can tell, it only removes the last track in the queue when any track in the queue has been played (i.e. it can remove the wrong track).<br/>
I made my own consume function that removes the last track played no matter where it is in the queue.  I'm not sure if I have reinvented the wheel; Volumio might already be able to do this.  For now, my consume function does the job.

All testers are welcome, even if they ride motorcycles.  You know who you are.

## Built with

* VS Code for debugging and coding.  I can't get over how good this editor is.
* vim for basic editing.  There is a lot of power in this humble editor.  You just have to believe....

## Acknowledgments

* Michelangelo and the other Volumio developers.
* lostmyshape gave me the heads-up about the Unofficial Pandora API and gave me some constructive ideas.  He was the first person to look at my code and give me help.  I also borrowed his mpd player listener callback function which really helped a lot.  Much obliged!
* marco79cgn, in particular, laid the groundwork for my plugin.  I shamelessly borrowed from his code in several areas.
* The creators of the other Volumio plugins.  I tried to learn from your code.
