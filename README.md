###### The project formerly known as 'My Echo Has Turrets Syndrome' (thank you forum) is back with a new name.

##### Introducting the all new:

![Download Now!](https://i.ibb.co/F56dQ2x/Alexa-Badge2.png)

# (Alexa has a) Comment for Everything.

<mark>"Amaze your Friends" "Endless Possibilities!"</mark>

Are you ready to add some excitement to your [**Home Assistant**](https://www.home-assistant.io) automations?! 

Make your Smart Home come alive! 

With **Comment for Everything** your Alexa will randomly comment about your home automation activities!

Would you like to hear Alexa speak '**good _____ (evening, afternoon, riddance)**' when a door closes? Or maybe she'll say **'brrr'** while your heater turns on?  Or perhaps Alexa will exclaim **'aww shucks'** when your glass break detector is triggered? 
___
(Alexa has a) **Comment For Everything** is for **Home Assistant**. It's absolutely free! ** Comment for Everything** Easy to setup (mostly cut and paste text)! Works with almost **ANY** automation (where the Everything comes from)! 

Here is a small sample of the things she'll say:

      - oh my
      - bah humbug
      - way to go
      - bam
      - oh snap
      - howdy
      - well
      - huh
      - bang
      - awesome

## How does this work?

Alexa has hundreds of *secret* special words and phrases that she pronounces more expressively! And most importantly when required, randomly. 

**Comment for Everything** incorporates nearly <mark>300</mark> of these words and phrases into **Home Assistant**! Phrases are spoken with emotion, clarity and confidence.  

## Features

- Now 100% Free! Absolutely no upfront costs or hidden fees. (was 3 easy payments of $39.99 plus hidden fees)
- Small file sizes. Very few lines of text to add. 
- Nearly 300 Special Words and Phrases!   
- Family friendly. Absolutely no distasteful or obscene language. 
- Foul language pack $12.99**
- Works with any number of Echo devices. Multiple Unlimited Echo devices!  (was 2 echo's free and $4.99 for each addtional echo device)
- New improved **AI** algorithm!
- Bonus **input_text** helper now included free of charge! ( a $9.99 value!)

** discontinued after various South American clean speach advocacy groups launched a massive ddos campaign against our servers. May still be available on warez sites (shhh! don't tell the Peruvian's).
 
##Source

We had to dig **deep** deep into the Echo developer documentation to unlock this secret feature and now we're bringing it to you absolutely free!

> "Hours of entertainment!"  "Suprising results!"[**] "Alexa finally has a soul" "Almost sentient!" 

 ** actual user comments

All that is needed is a service call to a simple script and you'll hear Alexa blurt a random exclamation **ANY** time you'd like. Live alone? **Comment for Everything** will make you feel as if someone else is there with you! Need an audible alert when an automation stops or starts? **Comment for Everything** will do the job. Need feedback when a light goes on?

___

Dust off those unused Echo devices. Unleash Alexa's hidden secret function in your smart home today! 

_____



## Requirements

- **Home Assistant** deployed and running
- **Alexa Media Player** integration
- Echo smart speaker (optional)

An Echo smart speaker is optional as the Alexa software can be installed on a laptop, computer, tablet, or smartphone.

# Installation

The **Comment for Everything** project consists of 2 helpers a few scripts and a single automation.  All you need to do is copy and past a few lines of text into each one of these files restart HA and you'll be hearing Alexa comments in no time!

## Helpers

 **1a**. IF you already have an **input_select.yaml** file: 

Update it with the helper text from **input_select.yaml** file above.  

Do the same for **input_text.yaml**.

**What does this do?**: The dropdown helper is the list of words and phrases Alexa already knows how to say more expressively. The text box will allow you to type anything you'd like alexa to say.

**1b**. If you do not have a file named **input_select.yaml** 

1. Download the input_select.yaml file and add it to your Home Assistant config directory. 

2. Add this line to **configuration.yaml**:
   
   ```yaml
   input_select: !include input_select.yaml
   ```
   
   Do the same for **input_text.yaml**

**Note**: If you updated **configuration.yaml** you'll need to restart Home Assistant.

## Scripts

**Step 2**. Add the following **3 ** scripts. to your **scripts.yaml** file 

Change the list of echos to entity id(s) of your own device(s). 

Save

**Note:** Use caution when editing any files directly. Making a **backup** of the file *before editing* can save you from getting into trouble.

**What does this do?**: The scripts send the word or phrase from the helper to your echo device which will say it.

```yaml
# List each echo device you want to use after the for_each line below

comment_for_everything_devices:
  sequence:
    - repeat:
        for_each:
          - media_player.echo_show_5
          - media_player.onelink_safe_sound
          - media_player.fire_tv
        sequence:
          - condition: template
            value_template: "{{ states('input_select.comment_for_everything') != '' }}"
          - service: notify.alexa_media
            data_template:
              message: >-
                <say-as interpret-as="interjection">{{
                states('input_select.comment_for_everything') }}</say-as>
              data:
                type: tts
              target: "{{ repeat.item }}"

# the script below will pick a RANDOM COMMENT from the dropdown.  
# The automation.comment_for_everything will 
# pick it up and send it to the script above.

comment_for_everything:
  sequence:
  - service: input_select.select_option
    target:
      entity_id: input_select.comment_for_everything
    data:
      option: "{{ state_attr('input_select.comment_for_everything', 'options')\n    | reject('eq',\
        \ states('input_select.test'))\n    | list | random }}\n"

# the script below is for the text helper. 
# again list each echo device you'd like to hear

alexa_comment:
  sequence:
    - repeat:
        for_each:
          - media_player.echo_show_5
          - media_player.onelink_safe_sound
          - media_player.fire_tv
        sequence:
          - condition: template
            value_template: "{{ states('input_text.alexa_comment') != '' }}"
          - service: notify.alexa_media
            data_template:
              message: >-
                <say-as interpret-as="interjection">{{
                states('input_text.alexa_comment') }}</say-as>
              data:
                type: tts
              target: "{{ repeat.item }}"
```

## Automation

This automation will be triggered anytime there is a change to the helpers. IE when you press enter after you have typed into the text helper or when you've run the **script.comment_for_everything**

```
- id: 'comment_for_everything'
  alias: Comment For Everything
  description: Triggered by helpers then outputs to scripts.
  trigger:
    - platform: state
      entity_id: input_select.comment_for_everything
      id: alexa-select
    - platform: state
      entity_id: input_text.alexa_comment
      id: alexa-text
  condition: []
  action:
    - choose:
        - conditions:
            - condition: trigger
              id: alexa-select
          sequence:
            - service: script.comment_for_everything_devices
              data: {}
        - conditions:
            - condition: trigger
              id: alexa-text
          sequence:
            - service: script.alexa_comment
              data: {}
  mode: queued
```

Step 5. Using the GUI create a text helper called Alexa Comment
or cut and paste code from file above.

Step 6. Check your configuration with Developer Tools.

Step 8. Restart HA if you get a passing score!

___

After HA is back up and running test it by running:

    script.comment_for_everything.

___

# Using Comment for Everything

Lets see if she can say some stuff....

## Random Comments

 After you've installed the project files just add the following line to **ANY** automation after the **action:**

```yaml
  - service: script.comment_for_everything
    data: {}
```

**What does this do?** Every time this script is run your Echo device(s) will exclaim something randomly from the input_select helper you created in Step 1. 

## Assigning Specific Word or Phrase

To assign a specific word or phrase to an automation do so in the following way:

    action:
      - service: input_select.select_option
        data:
          option: good morning # remove 'good morning' and type your chosen word or phrase there
        target:
          entity_id: input_select.comment_for_everything

## Manually Choosing A Phrase

Any time you'd like to hear Alexa pipe up (pipe up? really?) browse to a lovelace page containing the dropdown and select a new word or phrase. The automation will be triggered to run the script.

![Manual Operation](https://i.ibb.co/G37BqFv/Alexa-Chooser.gif)
 and you've got that witty broad talking again.

# Lovelace Buttons & Icons

Put some make-up on this mouthy woman.

## Button Card

How about a *custom:button-card* for a lovelace page?
![Preview of the button-card](https://i.ibb.co/0CdB08m/button-card-image.png)

```yaml
type: custom:button-card
entity: script.comment_for_everything
show_name: false
tap_action:
  action: call-service
  service: script.comment_for_everything
tooltip: Alexa Comments
show_entity_picture: false
show_state: false
show_icon: false
styles:
  card:
    - background: >-
        linear-gradient(0deg,  dimgray 0%, darkgray 41%, gainsboro 59%,
        ghostwhite 100%), url(https://i.ibb.co/Xyx3ndG/alexa-blue-80.png)
    - border: 4px solid dimgray
    - border-width: 1px 4px 2px 4px
    - border-top: 6px solid black
    - background-blend-mode: multiply
    - border-radius: 25px
    - height: 5em
    - width: 5em
    - outline: 4px dashed dimgray
```

##____

## ## Lovelace Custom Icons

![enter image description here](https://i.ibb.co/8cLS2Yg/com48b.png)

If you do not already have a file **customize.yaml** ~~go ahead and create it.~~ download the project file above and add it to your Home Assistant config directory.

**OR**

Add the following to your existing **customize.yaml**: 

    script.comment_for_everything:
      entity_picture: https://i.ibb.co/DwjjYBF/com40c.png
    script.comment_for_everything_devices:
      entity_picture: https://i.ibb.co/m8YS0Zk/com50c.png
    automation.comment_for_everything:
      entity_picture: https://i.ibb.co/DwjjYBF/com40c.png
    script.alexa_comment:
      entity_picture: https://i.ibb.co/m8YS0Zk/com50c.png
    input_select.comment_for_everything:
      entity_picture: https://i.ibb.co/DwjjYBF/com40c.png
    input_text.alexa_comment:
      entity_picture: https://i.ibb.co/m8YS0Zk/com50c.png

A **restart** is required to see results when you edit **customize.yaml**...

The Alexa Developer Documentation links have the information that is used for this project starts [here](https://developer.amazon.com/en-US/docs/alexa/custom-skills/speechcon-reference-interjections.html).

# Testimonials

What people are saying:

> "**Comment for Everything** is an essential addition to my Smart Home. It makes **Home Assistant** 1000 times more useful." Betty S. - Chicago IL 

> "The whole family loves how they never know *when* or *what* Alexa will say next! I have **Comment for Everything** setup in over twenty **Home Assistant** automations."  Bob J. - Estonia (near russian border)

> "Last night when I closed the garage Alexa said 'man overboard' and my wife thought the Echo was broken! We laughed for hours. Thank you **Comment for Everything**. You saved my marriage." Jason T. - Ohio

> "**Comment for Everything** is fantastic! Alexa spits out a random phrase each time my outdoor motion sensor goes off.  Maybe it's another racoon in the trash? Who knows?" Terrance W. East Sloon GA

![](https://i.ibb.co/pWCgwkT/com300.png)

---

## See Also

![Sounds for Everything](https://i.ibb.co/bK5stFp/SOUNDS-WAVE.png)

(Alexa has) Sound Effects for Everything

Over 200 Sound Effects! Multiple Devices Supported.  Bring **[Home Assistant](https://nabucasa.org)** to Life!!!
