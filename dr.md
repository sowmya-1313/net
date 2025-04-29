```tcl

#Create a simulator object
set ns [new Simulator]
$ns color 1 Blue
$ns color 2 Red
#Open the nam trace file
set nf [open out.nam w]
$ns namtrace-all $nf
#Define a 'finish' procedure
proc finish {} {
global ns nf
$ns flush-trace
#Close the trace file
close $nf
#Executenam on the trace file
exec nam out.nam &
exit 0
}
#Create four nodes
set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]


#Create links between the nodes
$ns duplex-link $n0 $n2 3Mb 10ms DropTail
$ns queue-limit $n0 $n2 50
$ns duplex-link $n1 $n2 3Mb 10ms DropTail
$ns queue-limit $n1 $n2 50
$ns duplex-link $n2 $n3 1Mb 10ms DropTail
$ns queue-limit $n2 $n3 10

$ns duplex-link-op $n0 $n2 orient right-down
$ns duplex-link-op $n1 $n2 orient right-up
$ns duplex-link-op $n2 $n3 orient right
$ns duplex-link-op $n2 $n3 queuePos 0.5

#Create a TCP agent and attach it to node n0
set tcp0 [new Agent/TCP]
$ns attach-agent $n0 $tcp0
#Create a TCP Sink agent (a traffic sink) for TCP and attach it to node n3
set sink1 [new Agent/TCPSink]
$ns attach-agent $n3 $sink1
#Connect the traffic sources with the traffic sink
$ns connect $tcp0 $sink1
$tcp0 set packetSize_ 1500
$tcp0 set fid_ 1

#Create a UDP agent and attach it to node n0
set udp2 [new Agent/UDP]
$ns attach-agent $n1 $udp2
#Create a TCP Sink agent (a traffic sink) for TCP and attach it to node n3
set null3 [new Agent/Null]
$ns attach-agent $n3 $null3
#Connect the traffic sources with the traffic sink
$ns connect $udp2 $null3
$udp2 set packetSize_ 1500
$udp2 set fid_ 2

#setup FTP
set ftp0 [new Application/FTP]
$ftp0 attach-agent $tcp0
$ns at 1.0 "$ftp0 start"
$ns at 8.0 "$ftp0 stop"

# Create a CBR traffic source and attach it to tcp0
set cbr0 [new Application/Traffic/CBR]
$cbr0 set packetSize_ 500
$cbr0 set interval_ 0.01
$cbr0 attach-agent $tcp0
#Schedule events for the CBR agents
$ns at 0.5 "$cbr0 start"
$ns at 4.5 "$cbr0 stop"
#Call the finish procedure after 5 seconds of simulation time
$ns at 5.0 "finish"
#Run the simulation
$ns run

