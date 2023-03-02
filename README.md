The project formerly known as 'My Echo Has Turrets Syndrome' (thank you forum) is back with a new name. Introducting the all new:

# (Alexa has a) Comment for Everything.

Are you ready to add some excitement to your **Home Assistant** automations?! Make your Smart Home come alive! With **Comment for Everything** your Alexa can share her thoughts about your home automation activities.  Would you like to hear alexa exclaim 'good morning' with emotion when you close a door? Or maybe she'll say 'brrr' when your heater turns on?  Or perhaps she'll exclaim 'aww shucks' when your glass break detector is triggered? 

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



Alexa has hundreds of secret special words and phrases that she pronounces more expressively! And most importantly randomly. **Comment for Everything** incorporates nearly 300 of these words and phrases into **Home Assistant**! Phrases are spoken with emotion and not in a flat drone. Works with almost any Echo device! She speaks with emotion, clarity and confidence.  We had to dig deep deep into the Echo developer documentation to unlock this secret feature and now we're bringing it to you absolutely free!

"Endless possibilities!" "Hours of entertainment!" "Amaze your friends!" "Suprising results!"

All that is needed is a service call to a simple script and you'll hear Alexa blurt a random exclamation ANY time you'd like. Live alone? **Comment for Everything** will make you feel as if someone else is there with you! Need an audible alert when an automation stops or starts? **Comment for Everything** will do the job. Need feedback when a light goes on?

Look no further. (Alexa has a) **Comment For Everything** is for **Home Assistant** and it's absolutely free! **Comment for Everything** is easy to setup! And works with almost ANY automation (where the Everything comes from)! 

Unleash Alexa's hidden secret function in your smart home today! 

## Requirements

 - **Home Assistant** deployed and running
 - **Alexa Media Player** integration
 - Echo smart speaker (optional)

An Echo smart speaker is optional as the Alexa software can be installed on a laptop, computer (windows only?), or smartphone.

## Features


 - 100% Free! Absolutely no upfront costs or hidden fees.
 -  Small file sizes. Very few lines of text to add. 
 - Nearly 300 Special Words and Phrases!   
 - Family friendly. Absolutely no distasteful or obscene language. 
 - Works with any number of Echo devices. Multiple Unlimited Echo devices!
 - Bonus input_text helper now included free of charge! 

# Installation

The project consists of 2 helpers a few scripts and a single automation.  All you need to do is copy and past a few lines of text into each one of these files restart HA and you'll be hearing Alexa comments in no time!

## Steps

 **1a**. If you already have an **input_select.yaml** file: Update it with the helper text from **input_select.yaml** file above.  Do the same for **input_text.yaml**.

**Explanation**: The dropdown helper is the list of words and phrases Alexa already knows how to say more expressively. The text box will allow you to type anything you'd like alexa to say.

**1b**. If you do not have a file named **input_select.yaml** download the project file and add it to your Home Assistant and  add this line to configuration.yaml. Do the same for **input_text.yaml**
```yaml
input_select: !include input_select.yaml
```

**Step 2**. Add the following 3 scripts. to your **scripts.yaml** file and save. Changing the target to an entity id of your own device. 
The File Editor add-on is as easy as anything. 

**Note:** Use caution when editing any of these files directly. Making a backup of the file before editing can save you from getting into trouble.

**Explanation**: The scripts send the word or phrase from the helper to your echo device which will say it.
```yaml
comment_for_everything:
  alias: Random Comment for Everything.
  sequence:
  - service: input_select.select_option
    target:
      entity_id: input_select.alexa_comments
    data:
      option: "{{ state_attr('input_select.alexa_comments', 'options')\n    | reject('eq',\
        \ states('input_select.test'))\n    | list | random }}\n"
alexa_comments:
  alias: Comment for Everything Actions
  sequence:
  - condition: template
    value_template: '{{ states(''input_select.alexa_comments'') != '''' }}'
  - service: notify.alexa_media
    data_template:
      message: <say-as interpret-as="interjection">{{ states('input_select.alexa_comments')
        }}</say-as>
      data:
        type: tts
      target: media_player.echo_show_5 # Change to your alexa device
      
alexa_freestyle_comment:
  alias: Alexa Freestyle Comment
  sequence:
  - condition: template
    value_template: "{{ states('input_text.alexa_freestyle_comment') != '' }}"
  - service: notify.alexa_media
    data_template:
      message: >-
        <say-as interpret-as="interjection">{{ states('input_text.alexa_freestyle_comment')
        }}</say-as>
      data:
        type: tts
      target: media_player.echo_show_5 #Change to your echo device
      
```
Be sure to change the target to the name of one of your own actual echo devices.

The main script is what chooses randomnly what your Echo device(s) will exclaim from the input_select helper you created in Step 1. 





**Step 3.** Add the automation.  

**Explanation**: The automation waits for a helper to change and then runs the corresponding script.

~~Using the file editor add this to the bottom of your **automation.yaml** file.~~  
Create an new automation in the GUI and paste over the whole thing with the following:

```yaml
alias: Comment For Everything
description: Triggered by inputs then outputs to smart speaker.
trigger:
  - platform: state
    entity_id: input_select.alexa_comments
    id: alexa-select
  - platform: state
    entity_id: input_text.alexa_freestyle_comment
    id: alexa-text
condition: []
action:
  - choose:
      - conditions:
          - condition: trigger
            id: alexa-select
        sequence:
          - service: script.alexa_comments
            data: {}
      - conditions:
          - condition: trigger
            id: alexa-text
        sequence:
          - service: script.alexa_freestyle_comment
            data: {}
mode: queued
```
This automation will be triggered anytime there is a change to the helpers. IE when you press enter after you have typed into the text helper or when you've run the **script.comment_for_everything**

Step 5. Using the GUI create a text helper called Alexa Freestyle Comment
or cut and paste code from file above.

Step 6. Check your configuration with Developer Tools.
Step 8. Restart HA if you get a passing score!

After HA is back up and running test it by running:

	script.comment_for_everything.

# Using Comment for Everything

## Random Comments

 After you've installed the project files just add the following line to ANY automation after the **action:**
```yaml
  - service: script.comment_for_everything
    data: {}
``` 
Every time this script is run your Echo device(s) will exclaim something randomly from the input_select helper you created in Step 1. 

## Assigning Specific Word or Phrase
To assign a specific word or phrase to an automation do so in the following way:

    action:
      - service: input_select.select_option
        data:
          option: good morning # remove 'good morning' and type your chosen word or phrase there
        target:
          entity_id: input_select.alexa_comments



# Button Card

How about a button-card for a lovelace page?
![Preview of the button-card](https://i.ibb.co/0CdB08m/button-card-image.png)
```yaml
type: custom:button-card
entity: script.comment_for_everything
show_name: false
tap_action:
  action: call-service
  service: script.comment_for_everything
tooltip: Alexa Comments
icon: mdi:account-voice
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


Using the File Editor add-on to add the following to customize.yaml: Note: If you do not already have a file 'customize.yaml' go ahead and create it.


    script.alexa_comments:
      entity_picture: https://i.ibb.co/4Tj5syQ/alexaq.png
    script.comment_for_everything:
      entity_picture: https://i.ibb.co/4Tj5syQ/alexaq.png

A restart is required to see results when you edit customize.yaml...

The Alexa Developer Documentation links have the information that is used for this project starts [here](https://developer.amazon.com/en-US/docs/alexa/custom-skills/speechcon-reference-interjections.html).