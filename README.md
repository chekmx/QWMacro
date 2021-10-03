{
  "_id": "gmnFySM4yWIHWIpW",
  "name": "QuestWorld Success Roll",
  "type": "script",
  "sort": 100001,
  "flags": {
    "furnace": {
      "runAsGM": false
    },
    "exportSource": {
      "world": "colymar-campaign",
      "system": "archmage",
      "coreVersion": "0.7.9",
      "systemVersion": "1.9.1"
    }
  },
  "scope": "global",
  "command": "new Dialog({\n  title:'Determine Success Level',\n  content:`\n    <form>\n      <div class=\"form-group\">\n        <label>Target Number</label>\n        <input type='text' name='targetNumberField'></input>\n      </div>\n      <div class=\"form-group\" class=\"collapsible\">\n        <label>Hinderance</label>\n        <input type='number' name='hinderanceField'></input> \n        <label>Benefit</label>\n        <input type='number' name='benefitField'></input>\n        please input level of hinderance or benefit as blank for none or the number of degrees i.e. 1 = +5 benefit or 2 = 10 hinderance\n      </div>\n      <div class=\"form-group\" class=\"collapsible\">\n        <label>Situational Modifiers</label>\n        <select name=\"situationalModifier\" id=\"situationalModifier\">\n           <option value=\"-10\">one degrees penalty</option>\n           <option value=\"-5\">zero degree penalty</option>\n           <option value=\"0\" selected>none</option>\n           <option value=\"5\">zero degree bonus</option>\n           <option value=\"10\">one degrees bonus</option>\n        </select>\n      </div>\n      <div class=\"form-group\" class=\"collapsible\">\n        <label>Augment Modifier</label>\n        <select name=\"augmentModifier\" id=\"augmentModifier\">\n           <option value=\"0\" selected>none</option>\n           <option value=\"5\">zero degree bonus</option>\n           <option value=\"10\">one degrees bonus</option>\n        </select>\n      </div>\n      <div class=\"form-group\">\n        <label>Strech Modifier</label>\n        <select name=\"stretchModifier\" id=\"stretchModifier\">\n           <option value=\"0\" selected>none</option>\n           <option value=\"-5\">one degree penalty</option>\n           <option value=\"-10\">two degrees penalty</option>\n        </select>\n      </div>\n    </form>`,\n  buttons:{\n    yes: {\n      icon: \"<i class='fas fa-check'></i>\",\n      label: `Roll!`\n    }},\n  default:'yes',\n  close: html => {\n    let result = html.find('input[name=\\'targetNumberField\\']');\n    let benefit= html.find('input[name=\\'benefitField\\']');\n    let hinderance= html.find('input[name=\\'hinderanceField\\']');\n    let modifier = html.find('select[name=\\'situationalModifier\\']').val();\n    let stretch = html.find('select[name=\\'stretchModifier\\']').val();\n    let augment= html.find('select[name=\\'augmentModifier\\']').val();\n    if (result.val()!== '') {\n       let res = result.val().toUpperCase().split(\"M\");\n       let tn = +res[0];\n           let masteryCount = 0;\n           if(tn <= 20) {\n             masteryCount = (res.length == 2) ? (res[1] !== '') ? res[1] : 1  : 0;\n             tn = (masteryCount * 20) + tn\n           }\n           let benefitMod = ((benefit.val() == \"\") ? 0 : benefit.val()) * 5\n           let hinderanceMod = ((hinderance.val() == \"\") ? 0 : hinderance.val()) * -5\n           //Calculate final Target number with all bonuses\n           tn = +tn + + modifier + +stretch + +augment + +benefitMod + +hinderanceMod \n           if(tn > 0){\n              masteryCount =  (tn  - tn % 20) / 20\n              tn = tn - (20 * masteryCount) \n              if(tn == 0){\n                 masteryCount = masteryCount -1;\n                 tn =20\n              }\n              let roll = new Roll(\"1d20\");\n              roll.evaluate();   \n              let successes = roll.result < tn ? 1 : (roll.result == tn) ? 2 : 0;        \n              successes = successes + masteryCount    \n              let successDisplay = successes == 1 ? \"SUCCESS\" : \"SUCCESSES\";          \n              let masterCountToDisplay = tn == 0 ? masteryCount -1 : masteryCount \n              let masteryDisplay = (masterCountToDisplay == 0) ? \"\" : (masterCountToDisplay  == 1 ) ? \"M\" : \"M\" + masterCountToDisplay;\n              let chatData = {\n                  type: CONST.CHAT_MESSAGE_TYPES.ROLL,\n\t          user: game.user._id,\n\t          speaker: ChatMessage.getSpeaker(),\n                  roll: roll,\n                  rollMode: game.settings.get(\"core\", \"rollMode\"),\n\t          content:  `<p><b>ACHIEVED ${successes} ${successDisplay}</b><br>  \n                             Against Target <b>${tn}${masteryDisplay}</b>\n                             with a roll of <b>${roll.result}</b></p>`\n\t      };\n\t      ChatMessage.create(chatData);\n           } else {\n              let chatData = {\n                   user: game.user._id,\n\t           speaker: ChatMessage.getSpeaker(),\n                   content:  `<p>Yout target number is zero or less.  You have no chance of success.</p>`\n              }\n              ChatMessage.create(chatData);\n           }\n    }\n}}).render(true);",
  "author": "x1M6Y2mWBNcWvJaF",
  "img": "icons/svg/d20-highlight.svg",
  "actorIds": []
}
