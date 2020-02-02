```
start => {
    display = bpm;
    gate = 0;
    notes = [];
    cv = 0;
}

arpegio => {
    cv = []
    gate on/off
}

[A..D as x].press => {
    if(!arp) {
        cv = x.note;
        gate = 1;
        notes.push();

        display = cv;
    } else {
        arpegio.push();

        if(arpegio.length == 1) {
            start_arp();
        }

        if(notes.lengt == 1) {
            transpose = 0;
        }

        if(notes.length > 1) {
            display = transpose;
        } else {
            display = x.note;
        }
    }
}

[A..D as x].release => {
    if(!hold) {
        notes.search_and_destroy(x.note);
        arpegio.search_and_destroy(x.note);

        if(notes.length == 0) {
            gate = 0;
            display = bpm;
        } else {
            cv = notes.last.note;
        }

        if(arpegio.length == 0) {
            stop_arp();
        }
    }

    if(notes.length > 1) {
        display = transpose;
    } else {
        display = x.note;
    }
}

hold_button.press => {
    hold = !hold;

    if(!hold && !A..D.some()) {
        notes.clear();
        arpegio.clear();
        gate = 0;
        arp_stop();
    }
}

arp_button.press => {
    arp = !arp;

    if(arp) {
        start_arp();
    } else {
        stop_arp();
    }
}

encoder.update(dir) => {
    if(!arp) {
        if(notes.length > 0) {
            notes.last.note += dir;
            A..D[notes.last.button] += notes.last.note;
            cv = notes.last.note;
            display = cv;
        } else {
            bpm += dir;
            display = bpm;
        }
    } else {
        for (i in 0..notes.length) {
            notes[i].note += dir;
            A..D[notes[i].button].note += notes[i].note;
            arpegio[i] += dir;
        }
        transpose += dir;
    }

    if(hold_button) {
        if(notes.length == 0){
            hold_time = gate_times.average;
            hold_time += dir;

            for gate in gate_times {
                gate = hold_time;
            }
        } else {
            notes.last.gate_time += dir;
        }
    }
}

led_hold = hold;
led_arp = arp;

```
