# Rust and Safety

I CAN'T MEME. THIS TEXT IS DEAD. Something something dinosaurs

As I spend more and more time writing and maintaining safety critical software I started to look into alternatives to using C.
While C does allow me to write fast and reliable software I often had to spend a lot of time chasing strange bugs
caused by some kind of undefined behavior introduced by one of my coworkers (I never write bad code).
Considering this, finding rust felt like a godsend, as being both fast and safe seems to exactly fit my requirements. So I went
and implemented a simple prototype in my spare time. While I had some smalls fights at the beginning,
especially when trying to borrow something, I did always come out on top. Therefore I ended up with exactly what rust promised me,
even if it was a rather easy challenge and could also be surpassed by using other tools.

I then talked with my supervisor about rust and if we could use it in production i