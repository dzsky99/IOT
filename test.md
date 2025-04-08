<details>
  <summary>TinyOS 실습</summary>

```C

#include "Timer.h"

module BlinkC @safe() {
    uses interface Timer<TMilli> as Timer0;
    uses interface Leds;
    uses interface Boot;
}

implementation {
    event void Boot.booted() {
        call Timer0.startPeriodic( 250 );
    }

    event void Timer0.fired() {
        call Leds.led0Toggle();
    }
}

```
#include "Timer.h"

module BlinkC @safe() {
    uses interface Timer<TMilli> as Timer0;
    uses interface Leds;
    uses interface Boot;
}

implementation {
    event void Boot.booted() {
        call Timer0.startPeriodic( 250 );
    }

    event void Timer0.fired() {
        call Leds.led0Toggle();
    }
}

```C

  
</datails>

<details>
  <summary>TinyOS 실습2</summary>

```C

#include "Timer.h"

module BlinkC @safe() {
    uses interface Timer<TMilli> as Timer0;
    uses interface Timer<TMilli> as Timer1;
    uses interface Timer<TMilli> as Timer2;
    uses interface Leds;
    uses interface Boot;
}

implementation {
    event void Boot.booted() {
        call Timer0.startPeriodic( 250 );
        call Timer1.startPeriodic( 500 );
        call Timer2.startPeriodic( 1000 );
    }

    event void Timer0.fired() {
        call Leds.led0Toggle();
    }
    event void Timer1.fired() {
        call Leds.led1Toggle();
    }
    event void Timer2.fired() {
        call Leds.led2Toggle();
    }
}

```

```C

configuration BlinkAppC {
}
implementation {
    components MainC, BlinkC, LedsC;
    components new TimerMilliC() as Timer0;
    components new TimerMilliC() as Timer1;
    components new TimerMilliC() as Timer2;

    BlinkC -> MainC.Boot;
    // child -> parent
    BlinkC.Timer0 -> Timer0;
    BlinkC.Timer1 -> Timer1;
    BlinkC.Timer2 -> Timer2;
    BlinkC.Leds -> LedsC;
}

```
</datails>
