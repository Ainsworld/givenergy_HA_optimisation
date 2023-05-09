# Home Assistant Givenergy / Octopus Go optimisations
Setup for Home Assistant to optimise operations with Octopus Go tariff

This documents three parts of what I've set up on Home Assistant that ensure our household gets the optimal value for money from our setup which consists of
 - a solar panel array
 - a storage battery
 - an electric vehicle
whilst using a tarrif (like Octopus Go) which consists of a very cheap off-peak period and higher prices the rest of the time.

Also true of our tariff is that although we receive a modest income from selling surplus solar to the grid, this is at a lower price than we pay to buy from the grid even at the lowest off-peak time. This means that the overall optimum is always to consume whatever solar energy is generated if at all possible. This informs the three key parts of the setup:

## 1. Battery target

Each night, just before the start of the off-peak period, the target State Of Charge (SOC) for the battery is set to store...
 - As much energy as possible AND
 - To leave enough spare space to hold all surplus solar energy expected to be generated in the coming day (taken from Solcast)...
 - allowing for expected household usage in the part of the coming day up until the sun stops driving generation

This is achieved with a calculation in configuration.yaml, an input number to define household usage, and an automation that sets the SOC according to the calculation.

## 2. 'Bleed off' smart plug

Despite optimisation point 1, some days there can be too much solar. (Maybe the battery was already full from yesterday. Maybe it's just very sunny relative to battery capacity.) 

Once an hour the smart plug is switched on if its usage wouldn't cause the house battery to get exhausted by the end of the day. The anticipated usage here would be to drive a three-pin car charger. This hourly evaluation depends on...
 - An hourly-updated calculation of how much usage the house is likely to use in a high-usage scenario between that time and the end of the day
 - The projected solar power generated from present time to the end of the day (a Solcast forecast entity)
 - An input number specifying the expected usage over an hour from whatever is plugged into the smart plug.
 
In effect, the plug only turns on when the battery has plenty of spare space without risk of running out even after the plug is on for an hour. And it continues to stay on until this extra draw gets the battery to the point where it just has enough to run the house for the rest of the day.

## 3. Warning of surplus solar

Given that a household might only rarely have enough surplus solar that you'd tap some off with the smart plug (under optimisation 2) then there's also a simple automation that raises an alert in the morning when it's anticipated that there will be surplus solar in the day, so you know to plug the EV into the charger ready to be filled using optimisation 2.
