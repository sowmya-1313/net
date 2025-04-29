```tcl

4 bus
set ns [new Simulator]
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
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
set lan0 [$ns newLan "$n0 $n1 $n2 $n3 $n4" 0.5Mb 40ms LL Queue/DropTail MAC/Csma/Cd Channel]

set tcp [new Agent/TCP]
$ns attach-agent $n0 $tcp

set sink [new Agent/TCPSink]
$ns attach-agent $n3 $sink
$ns connect $tcp $sink

set cbr [new Application/Traffic/CBR]
$cbr attach-agent $tcp
$cbr set packet_size_ 500
$cbr set interval_ 0.01

$ns at 0.1 "$cbr start"
$ns at 4.5 "$cbr stop"
$ns at 5.0 "finish"

$ns run


4ring
set ns [new Simulator]
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
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
$ns duplex-link $n0 $n1 2Mb 10ms DropTail
$ns duplex-link $n0 $n2 2Mb 10ms DropTail
$ns duplex-link $n2 $n3 2Mb 10ms DropTail
$ns duplex-link $n3 $n4 2Mb 10ms DropTail
$ns duplex-link $n4 $n1 2Mb 10ms DropTail
set tcp [new Agent/TCP]
$ns attach-agent $n0 $tcp
set sink0 [new Agent/TCPSink]
$ns attach-agent $n3 $sink0
$ns connect $tcp $sink0
set cbr [new Application/Traffic/CBR]
$cbr attach-agent $tcp
$cbr set packet_size_ 500
$cbr set interval_ 0.01
$ns at 0.1 "$cbr start"
$ns at 4.5 "$cbr stop"
$ns at 5.0 "finish"
$ns run


4star
# Create the simulator
set ns [new Simulator]
$ns color 1 Blue
$ns color 2 Red

# Open a NAM trace file
set nf [open out.nam w]
$ns namtrace-all $nf

# Define a procedure to finish the simulation
proc finish {} {
    global ns nf
    $ns flush-trace
    close $nf
    exec nam out.nam &
    exit 0
}

# Create 5 nodes: 1 central node (n0) and 4 leaf nodes (n1, n2, n3, n4)
set n0 [$ns node]  ; # Central node
set n1 [$ns node]  ; # Leaf node 1
set n2 [$ns node]  ; # Leaf node 2
set n3 [$ns node]  ; # Leaf node 3
set n4 [$ns node]  ; # Leaf node 4
set n5 [$ns node]  ; # Leaf node 5

# Set up duplex links between the central node (n0) and each leaf node (n1, n2, n3, n4)
$ns duplex-link $n0 $n1 2Mb 10ms DropTail
$ns duplex-link $n0 $n2 2Mb 10ms DropTail
$ns duplex-link $n0 $n3 2Mb 10ms DropTail
$ns duplex-link $n0 $n4 2Mb 10ms DropTail
$ns duplex-link $n0 $n5 2Mb 10ms DropTail

# Set the queue limits and other link properties for all connections
$ns queue-limit $n0 $n1 10
$ns queue-limit $n0 $n2 10
$ns queue-limit $n0 $n3 10
$ns queue-limit $n0 $n4 10

# Setting link orientations and queue positions (for visualization purposes)
$ns duplex-link-op $n0 $n1 orient right-down
$ns duplex-link-op $n0 $n2 orient right-down
$ns duplex-link-op $n0 $n3 orient right-down
$ns duplex-link-op $n0 $n4 orient right-down
$ns duplex-link-op $n0 $n1 queuePos 0.5
$ns duplex-link-op $n0 $n2 queuePos 0.5
$ns duplex-link-op $n0 $n3 queuePos 0.5
$ns duplex-link-op $n0 $n4 queuePos 0.5

# Set up UDP agent and Null agent for traffic generation between nodes
set udp [new Agent/UDP]
$ns attach-agent $n1 $udp
set null [new Agent/Null]
$ns attach-agent $n5 $null
$ns connect $udp $null
$udp set fid_ 2

# Set up CBR application
set cbr [new Application/Traffic/CBR]
$cbr attach-agent $udp
$cbr set type_ CBR
$cbr set packet_size_ 1000
$cbr set interval_ 0.01  ; # Set interval to 0.01 seconds
$cbr set rate_ 1mb
$cbr set random_ false

# Start the traffic and stop it at the specified times
$ns at 0.1 "$cbr start"
$ns at 4.5 "$cbr stop"

# End the simulation and clean up
$ns at 5.0 "finish"

# Print traffic details
puts "CBR packet size = [$cbr set packet_size_]"
puts "CBR interval = [$cbr set interval_]"

# Run the simulation
$ns run
