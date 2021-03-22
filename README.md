new Dialog({
  title:'Determine Success Level',
  content:`
    <form>
      <div class="form-group">
        <label>Target Number</label>
        <input type='text' name='targetNumberField'></input>
      </div>
      <div class="form-group">
        <label>Situational Modifiers</label>
        <select name="situationalModifier" id="situationalModifier">
           <option value="-10">two degrees penalty</option>
           <option value="-5">one degree penalty</option>
           <option value="0" selected>none</option>
           <option value="5">one degree bonus</option>
           <option value="10">two degrees bonus</option>
        </select>
      </div>
      <div class="form-group">
        <label>Strech Modifier</label>
        <select name="stretchModifier" id="stretchModifier">
           <option value="0" selected>none</option>
           <option value="-5">one degree penalty</option>
           <option value="-10">two degrees penalty</option>
        </select>
      </div>
    </form>`,
  buttons:{
    yes: {
      icon: "<i class='fas fa-check'></i>",
      label: `Roll!`
    }},
  default:'yes',
  close: html => {
    let result = html.find('input[name=\'targetNumberField\']');
    let modifier = html.find('select[name=\'situationalModifier\']').val();
    if (result.val()!== '') {
       let res = result.val().toUpperCase().split("M");
       let tn = +res[0];
           let masteryCount = 0;
           if(tn <= 20) {
             masteryCount = (res.length == 2) ? (res[1] !== '') ? res[1] : 1  : 0;
             console.log(masteryCount)
             tn = (masteryCount * 20) + tn
             console.log(tn)
           }
           tn = +tn + +modifier
           console.log(tn)
           masteryCount =  (tn  - tn % 20) / 20
           tn = tn - (20 * masteryCount) 
           let roll = new Roll("1d20");
           roll.evaluate();
           let successes = roll.dice[0].results.reduce((acc, die) => 
                       (die.result == tn) ? acc+=2 : (die.result <= tn) ? acc+=1 : acc, 0);
           successes = +successes + +masteryCount;

           let chatData = {
                  type: CHAT_MESSAGE_TYPES.ROLL,
	          user: game.user._id,
	          speaker: ChatMessage.getSpeaker(),
                  roll: roll,
                  rollMode: game.settings.get("core", "rollMode"),
	          content:  `<p><b>ACHIEVED ${successes} SUCCESSES</b><br>  
                             Against Target <b>${tn}M${masteryCount}</b>
                             with a roll of <b>${roll.result}</b></p>`
	   };
	   ChatMessage.create(chatData);
    }
}}).render(true);
