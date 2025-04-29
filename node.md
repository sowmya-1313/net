```tcl
set ns [new Simulator]
$ns color 1 blue
$ns color 2 pink
set nf [open out.nam w]
$ns namtrace-all $nf

proc finish {} {
    global ns nf
    $ns flush-trace
    close $nf
    exec nam out.nam &
    exit 0
}

set n0 [$ns node]
set n1 [$ns node]
$ns duplex-link $n0 $n1 2Mb 10ms DropTail
$ns queue-limit $n0 $n1 10
$ns duplex-link-op $n0 $n1 orient left
$ns duplex-link-op $n0 $n1 queuePos 0.5
$ns at 5.0 "finish"
$ns run

