#### reflections on trusting trust - Ken Thompson: [src](http://www.ece.cmu.edu/~ganger/712.fall02/papers/p761-thompson.pdf)
paper establishes, that no system that you did not fully create yourself can be trusted. it gives an example of a compiler, that works normally, but when it encounters the program "login", it will miscompile it so that it allows a hardcoded password. if you were to inspect the source and remove the miscompilation, it can also miscompile itself to reinsert the bug. no amount of source code verification fully protects you from bad actors.
same concept can be applied to any program handling program (assembler, linker or even cpu microcode)
##### thoughts
seems to be more applicable then ever with assertions that Intel ME has backdoors
##### rating: 0
have to start somewhere. it's interesting, short and doesn't require any prerequisites
