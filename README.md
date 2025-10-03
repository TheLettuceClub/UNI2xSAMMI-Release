# UNI2xSAMMI
 
This mod (intends to) export UNI2's game state to any app you wish that is capable of reading WebSockets data. In particular: SAMMI, the streaming assistant, is targeted.
Very WIP at the moment! Expect bugs, inconsistencies, and lack of data. Please get in contact (below), if you have questions/concerns!
Questions?: Hopefully the're answered [here!](https://thelettuceclub.github.io/mods/)

## Installation
1. Navigate to your UNI2 install directory. You can right click on the game in Steam and then click "browse local files" under "manage" to get to the right folder.
2. Put all three .dll files in the release zip into that folder, next to UNI2.exe
3. Launch the game. If a console window appears with some text as the game launches, then it's working.
4. Connect with your client of choice (port: 12524). This is implementation specific. SAMMI instructions to come soon (tm).

## Disclaimer
To preempt any misuse, an output delay of 300ms is prepended to every outgoing event.

This can be easily accommodated for in OBS if you're interested in synchronizing effects to gameplay. For video, use Render Delay filters. For audio, apply Delay to the appropriate sources in Advanced Audio Properties.

This mod is read-only, so it cannot be used to alter gameplay.

## API overview
The mod sends out N types of events based on the state of the game. They are detailed below.
Each event consists of a JSON object with multiple fields. Tabbing implies a '.'
<br><br>
#### State Update:
* Abstract: the general state of the game, occurs every frame.
* Fields:
    * data
        * event: "uni2_StateUpdate"
        * eventInfo
            * p1/2
                * CharacterID (this doesn't update if you use the training mode shortcut on the main menu)
                * health
                * exsMeter
                * state
                * stateBackup (in case the backing string for state isn't found)
                * posX
                * posY
                * inputs
                    * up, down, forwards, back (stick/movement values)
                    * A, B, C, D (buttons, macros just turn on multiple of these)
                * vorpal
                * veilOff
                * CVeilOff
                * GRDBlocks
            * frameCount

#### Hit Event:
* Abstract: fires every time any character gets hit
* Notes: also fires 1-2 times during knockdown animations. The data contained in the message is the same as the last hit of the combo (including comboCount), so you can ignore it based on that
* Fields:
    * data
        * event: "uni2_HitEvent"
        * eventInfo
            * p1/2 (same sub-fields as State Update)
            * attacker
            * defender
            * comboCount
            * lastHitDamage
            * comboDamage
            * meterGainedInCombo
            * comboProration
			* comboRecovery
            * frameCount

#### Vorpal Event:
* Abstract: fires whenever either character achives Vorpal state (i.e. the TS timer finishes).
* Fields:
    * data
        * event: "uni2_VorpalEvent"
        * eventInfo
            * p1/2 (same sub-fields as State Update)
            * whoWonGRD
            * timesWon
            * frameCount

#### Round Start:
* Abstract: fires whenever a round begins (also training mode reset)
* Fields:
    * data
        * event: "uni2_RoundStartEvent"
        * eventInfo: empty

#### Round End:
* Abstract: fires whenever a round ends
* Fields:
    * data
        * event: "uni2_RoundEndEvent"
        * eventInfo:
            * p1/2 (same sub-fields as State Update)
            * winner
            * loser
            * reason
            * frameCount
