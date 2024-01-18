var config = {
  bet: { label: 'bet', value: currency.minAmount*1, type: 'number' },
  basePayout: { label: 'base coef', value: 3, type: 'number' },
  skipFall: { label: 'skip games, coef less base', value: 10, type: 'number' },
  martingaleSteps: { label: 'martingale steps', value: 1, type: 'number' },
  games: { label: 'repeat algorithm', value: 20000000000000, type: 'number' },
};

var bet = config.bet.value;
var betNext = 0;
var betsSum = 0;
var bets = [];

var gamesplayed = 0;

function calcBets()
{
  var sum = 0;
  var profit = bet*config.basePayout.value;
  var lbet = bet
  bets = [];
  while(sum<currency.amount)
  {
    var tmpSum = sum+lbet;
    while(tmpSum>profit)
    {
      lbet *= 2;
      profit = lbet*config.basePayout.value;
      tmpSum = sum+lbet;
    }
    sum = tmpSum;
    if(sum<=currency.amount)
      bets.push(lbet);
    
if( config.martingaleSteps.value>0)
     {if(bets.length== config.martingaleSteps.value){break;}}
  }
  var allSteps = config.skipFall.value-1+bets.length;
  log.success(bets);
  log.success(bets.length);
  log.success("Max sequence steps - "+(allSteps));
  return bets.length;
}

var play = false;


function skipped()
{
  var history = engine.getHistory();
  var coef = config.basePayout.value*100;

  for(h = 0; h<config.skipFall.value; h++)
  {
    if(coef<=history[h].crash)
    {
      log.info(" " + (1+h)+"/"+config.skipFall.value+' waiting round');
      return false;
    }
  }
    
  return true;
}

function doBet()
{
  betsSum += bets[betNext];
  log.error((betNext+1)+'||'+bets.length+" "+betsSum+'/'+bets[betNext]*config.basePayout.value+' (profit '+(bets[betNext]*config.basePayout.value-betsSum)+')');
  engine.bet(bets[betNext++], config.basePayout.value );
  
}

function test(){
  calcBets();
}



function main () {
  log.success(currency.amount);
  var history = engine.getHistory()
  var lastGame = history[10]
  log.success(lastGame);
  engine.on('GAME_STARTING', function () {
    if(gamesplayed<config.games.value)
    {
    if(play){ if(betNext==bets.length){play=false;}}
    if(!play) {play = skipped(); if(play) { betNext = 0; betsSum = 0; bet = config.bet.value; calcBets();}}
    if(play)
    {
        doBet();
    }

    }

  });

engine.on('GAME_ENDED', function ()
  {
    var history = engine.getHistory()
    var lastGame = history[0]
    // If we wagered, it means we played
    if (!lastGame.wager) {
      return;
    }

    // we won..
    if (lastGame.cashedAt) {
      play = false;
      gamesplayed += 1;
      log.success('We won - '+ bets[betNext-1]*config.basePayout.value-betsSum) ;

    } else {
      //lose game
    }

  });
    

    
}
