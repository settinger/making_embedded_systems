# Final Project State Documentation

## Projective Set on the STM32F429I-DISC1

General idea: an implementation of [Projective Set](https://en.wikipedia.org/wiki/Projective_Set_(game)) with multiple difficulty settings, touchscreen and console input, colorblindness accessibility modes, and shake-to-reset function using the on-board motion sensors.

```mermaid
  graph TD;
      A-->B;
      A-->C;
      B-->D;
      C-->D;
```

```mermaid
graph stateDiagram-v2;
  [*] --> Still;
  Still --> [*];
  
  Still --> Moving;
  Moving --> Still;
  Moving --> Crash;
  Crash --> [*];
```