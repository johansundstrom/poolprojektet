# POOLPROJEKTET

Pool med filter pump och solfångare. Vid sol ska pumpen trycka vatten genom solfångaren för uppvärmning. Vid natt eller kallare temperatur ska pumpen vara av för att hindra avkylning. Pumpen är ansluten till Sonoff TH10 och 2x DS18B20 sensorer finns.

3 rules:

* Pumpen ska starta när solfångaren är mer än 2 grader varmare än pooltemperaturen
* Pumpen ska stoppa när solfångaren är mindre än 1 grad varmare än pooltemperaturen
* Pumpen ska inte starta om solfångaren är under 25 grader.

### Variabler

* t1: aktuell temperatur på poolvattnet
* t2: aktuell temperatur i solfångaren
* var1: in valid panel temp range?
* var2: off threshold temp for panel
* var3: on threshold temp for panel
* mem3: lägsta möjliga solfångartemp

```
mem3 25

rule1
  on DS18B20-1#temperature do
    event t1=%value%
  endon
  on DS18B20-2#temperature do
    event t2=%value%
  endon
  on event#t2>%mem3% do 
    var1 1;
  endon
  on event#t2<=%mem3% do 
    var1 0;
  endon
  on event#t1 do 
    backlog
    var2 %value%;
    add2 1;
  endon
  on event#t1 do 
    backlog
    var3 %value%;
    add3 2;
  endon
  on event#t2>%var3% do
    power1 %var1%;
  endon
  on event#t2<%var2% do
    power1 0;
  endon

rule1 1
```

```
on DS18B20-1#temperature do event t1=%value% endon
on DS18B20-2#temperature do event t2=%value% endon
on event#t2>%mem3% do var1 1; endon
on event#t2<=%mem3% do var1 0; endon
on event#t1 do backlog var2 %value%; add2 1; endon
on event#t1 do backlog var3 %value%; add3 2; endon
on event#t2>%var3% do power1 %var1%; endon
on event#t2<%var2% do power1 0; endon
```

To test the rule without having the sensors in place, simply enter the events for t1 and t2 in the console:
Backlog event t1=21;event t2=30

And watch the relay turn on and off based on the values.

Please note that this example does not support manual override or handle missing sensor data. Take a look at Thermostat Example for examples.


---

```
Rules2 on button1#state=1 do power1 1 enddo; on button1#state=0 do power1 0 enddo
```

* EventEventName - when command ```Event eventName``` is executed. You can define your own event values and trigger them with the ```Event``` command. An event with a ```=``` will provide a ```%value%``` to use in the execution part of the rule. Example: Command ```Event speed=2``` in rule trigger ```on event#speed``` will have the ```%value%``` of ```2```.
