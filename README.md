# Over the Hedge: Quantitative Betting Strategy for Live MLB Games

<img align="left" src="https://github.com/sbconlon/over-the-hedge/blob/main/images/over-the-hedge-logo.png" width=40%>

### Table of Contents
&nbsp;&nbsp;1. [Introduction](#1-introduction) <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1.1. Overview <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1.2. Game theoretic description <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1.3. Why baseball? <br/>
&nbsp;&nbsp;2. [Dataset](#2-dataset) <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2.1. Problem and description <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2.2. Collecting baseball state data <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2.3. Collecting odds state data <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2.4. Infering odds state from baseball state <br/>
&nbsp;&nbsp;3. [Model](#3-model) <br/>

<br/><br/><br/><br/>
## 1. Introduction
### 1.1. Overview
Over the Hedge is an algorithm for optimal wagering during live MLB baseball games. <br/>

Past approaches to applying machine learning to sports gambling markets have used a one-shot approach before the start of the game. That is, some model is used before the game begins to determine whether or not a wager should be made. The thesis of Over the Hedge is that a better return can be made if instead wagers are made through out the sporting event. This has the advantage of viewing the problem through a game theoretic approach where complex strategies can be computed that wouldn't otherwise be possible in the single bet paradigm. One such example is the ability to hedge during the game.

### 1.2. Game theoretic description

**Game description** - in-game live wagering can be formulated as a two-player zero-sum imperfect information game. <br/>

**Players**
 * _Bookmaker_ - for each timestep in the game, the bookmaker sets the price for each team. <br/><br/>
 * _Bettor_ - for each timestep, the bettor can choose to place a wager on a team at a given price and quantity. <br/><br/>

 Both players seek to maximize their expected profits at the direct expense of the other. <br/><Br/>

**Game state** <br/><br/>
The game state can be broken down into three parts: <br/><br/>
 * _Baseball state_ - all features that describe the current state of the baseball game.
   * Ex: score, inning, runners on base, pitcher, player stats, team rosters, etc. <br/><br/>
 * _Odds state_ - the price for each team set by the bookmaker for the current baseball state. <br/><br/>
 * _Bettor state_ - describes the bettor's current state including their bankroll and bets made in prior game states. <br/><br/>

 All combined these three parts fully encapsulate the information needed to describe the game. <br/><br/>

 **Player actions**<br/><br/>
  * _Bookmaker_ - at each timestep, the bookmaker must set a price for each team. This price is a value greater than 1 that sets the multiplier for the payout to a winning wager.
    * Ex: if the bettor wagers $100 on the home team at a price of 3 and the home team wins, then the bookmaker must payout $300 to the bettor. <br/><br/>
  * _Bettor_ - at each timestep, the bettor can choose to make a wager within the limits of their bankroll at the price set by the bookmaker, or choose to do nothing. <br/><br/>

  **Player incentives and strategy equilibriums**
   * _Bookmaker_ - the bookmaker must set their prices at an equilibrium. If prices are too low, then the bettor will not make wagers, limiting the potential profits that could be made. Conversely, if prices are too high, then the bettor will make too many winning wagers, costing the bookmaker greatly. <br/><br/>
   * _Bettor_ - the better must devise an optimal strategy such that they maximize their expected utility against a best response bookmaker, i.e. a Nash equilibrium.

### 1.3. Why baseball?
Baseball is an ideal application for computational game solving for four reasons. First, baseball has an advanced sabermetrics community that supplies many useful stats. Second, baseball is a descritized game. In general, each game state can be thought of as a batter-pitcher matchup. The batter eventually produces an outcome that transitions the current game state to the next, with a new batter-pitcher matchup. Thrird, baseball is slow. Each batter-pitcher mathcup takes on the order of a minute, providing ample time for an optimal wager strategy to be computed. Fourth, I love baseball and enjoy watching it. 

## 2. Dataset
### 2.1. Problem and description
The first problem encountered when working on this project is the need for a large dataset of game states to train an agent on. Furthermore, step-by-step game states for past MLB games with their accompaning bookmaker odds are not publicly available, or don't exist at all.

### 2.2. Collecting baseball state data
To collect the baseball states I built an algorithm to process old play-by-play data from past baseball games into state vectors. This project is called Retrofeats and can be found [here](https://github.com/sbconlon/retrofeats).

### 2.3. Collecting odds state data
To collect the odds statees I built an online web scraper to collect published odds from bookmakers as games were happening. I started running this scraper during the 2023 MLB season. The code for this project can be found at [MLB Scraper](https://github.com/sbconlon/mlb-scraper) repository.

### 2.4. Infering odds states from baseball states
The limitiations of the odds scraper, described in Section 2.3, is that it only provides a small set of odds states for recent games. In order to produce odds states for the backlog of past baseball states, built in Section 2.1, I trained a supervised learning model to predict the bookmaker odds given a baseball game state. This allows for a large dataset of baseball games, approximately 62,000, with realistic bookmaker odds to be used for training an agent. This project is called Market Mime. It is currently still under development and will be released soon. 

## 3. Model
Once the dataset is ready, I will build a computational game solving model inspired by the Student of Games, SoG, algorithm ([paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10651118/pdf/sciadv.adg3256.pdf) and [my notes](https://github.com/sbconlon/notes/blob/main/papers/student-of-games-a-unified-learning-algorithm-for-both-perfect-and-imperfect-info-games.pdf)). SoG unifies game solving methods for perfect and imperfect information games by combining search, learning, and game-theoretic reasoning. The two main components of SoG are Growing-Tree counterfactual regret minimization, GT-CFR, and sound self-play. SoG was chosen to be the inspiration for this model because of its strong performance on challenge domains such as heads-up no-limit poker, Scotland Yard, Go, and chess. 

