# Assembly as a Service

Be an Assembly shop. Taking a look at https://www.smallbatchassembly.com/, they're very much our vibe. But maybe we can do it  too!

I kinda like the idea of having just an obvious "thing to do" that doesn't require producing anything and is more a service. Then we just "turn the crank" and maybe that could be the basis for other projects?

It's also an easy story to sell with a clear goal.

## The Idea

0. Obtain the open pick and place machine
1. We take in orders that consist of a PCB design, a Bill of Materials (BOM), and like how many they want.
   * Work with customers to provide designs that work well with our assembly process
2. Components would be required to be available on a selection of vendors of our choosing
   * To support this, maybe we could make our own part search site that links to the vendors we would use?
3. Using the BOM, we generate an order to our vendors (perhaps semi-automatically) to source the components
4. We forward the PCB order directly to Osh Park using the design files
   * Do we work with Osh Park more directly and ask for a whole panel - depending on order size - or at least not to disconnect the boards from each other? Maybe it's easier to stencil that way?
5. Order a stencil from Osh Stencil
    * Optionally if the customer has a stencil they could send it maybe?
6. Once boards and components are obtained, Stencil+Solder Paste, Pick+Place, Solder Oven (taking care for dual sided boards), Some kind of verification checking, cleaning, package, ship

## Back of napkin calculations

### Board Cost
Osh Park Medium Run:
1$ per square inch, multiples of 10 boards, min 100 square inches, per 2-layers (up to only 4 layers I guess)

https://docs.oshpark.com/services/two-layer-medium-run/

So we can assemble in multiples of 10 as well.

### Stencil Cost:

$1.55 per square inch, plus some shipping

### Material Cost Total

Stencil + Boards + Components/board + generic overhead (solder, cleaner, etc)

### Labor

Say it takes H hours to complete a 10 board run. If this is operable by a single person, how much do we want to charge per board in assembly costs to grant the individual laboring a livable wage?

$5000 a month is probably undervalued, but is a modest starting point for curiosity at least. That's (call it) $32 / hour.

So we charge $32*H for each 10 board batch, passing on the material costs?

### Example:
Let's say we have a 5.08mm x 5.08mm board (this is 4 square inches) order, so this requires a min of 30 boards (to meet the min 100 sq in rule at Osh Park).

* Let components cost $2 per board.
* Stencil cost is $6.20 + say $6 shipping (made up)
* Let overhead charge be $5 per order?

Material Cost: $12.20 (Stencil) + $120 (boards) + $60 (Components) + $5 Overhead
Material Cost: 197.2

Say we can do 10 boards in 4 hours, so H=4.
Labor: $128

So we charge $325.20 for this order. This works out to be $10.84 per board for the customer.

Doing a quick "Instant Quote" on Small Batch Assembly, this is competitive it looks like. 

