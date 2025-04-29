2 exp
set ns [new Simulator]
$ns color 1 blue
$ns color 2 pink
set nf [open out.nam w]
$ns namtrace-all $nf
#Define a 'finish' procedure
proc finish {} {
        global ns nf
        $ns flush-trace
        #Close the NAM trace file
        close $nf
        #Execute NAM on the trace file
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
