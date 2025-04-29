```tcl


# Create a simulator object
set ns [new Simulator]

# Open the NAM trace file
set nf [open out.nam w]
$ns namtrace-all $nf

# Define a 'finish' procedure
proc finish {} {
    global ns nf
    $ns flush-trace
    # Close the NAM trace file
    close $nf
    # Execute NAM on the trace file
    exec nam out.nam &
    exit 0
}

# Create six nodes
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]
set n4 [$ns node]
set n5 [$ns node]
set n6 [$ns node]

# Create links between nodes
$ns duplex-link $n1 $n3 2Mb 10ms DropTail
$ns duplex-link $n2 $n3 2Mb 10ms DropTail
$ns duplex-link $n3 $n4 2Mb 10ms DropTail
$ns duplex-link $n4 $n5 2Mb 10ms DropTail
$ns duplex-link $n4 $n6 2Mb 10ms DropTail

# Orientation for NAM visualization
$ns duplex-link-op $n1 $n3 orient right-down
$ns duplex-link-op $n2 $n3 orient right-up
$ns duplex-link-op $n3 $n4 orient right
$ns duplex-link-op $n4 $n5 orient right-down
$ns duplex-link-op $n4 $n6 orient right-up

# Setup a TCP connection
set tcp [new Agent/TCP]
$tcp set class_ 1
$ns attach-agent $n1 $tcp

# Create a TCP Sink agent
set sink [new Agent/TCPSink]
$ns attach-agent $n4 $sink

# Connect TCP source to sink
$ns connect $tcp $sink

# Attach FTP application to TCP
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ftp set type_ FTP

# Setup a UDP connection
set udp [new Agent/UDP]
$ns attach-agent $n1 $udp

set null [new Agent/Null]
$ns attach-agent $n6 $null

# Connect UDP source to Null sink
$ns connect $udp $null

# Setup a CBR over UDP connection
set cbr [new Application/Traffic/CBR]
$cbr set packet_size_ 1000
$cbr set interval_ 0.01
$cbr attach-agent $udp

# Schedule events
$ns at 0.5 "$ftp start"
$ns at 1.0 "$cbr start"
$ns at 5.0 "$ftp stop"
$ns at 5.0 "$cbr stop"
$ns at 5.0 "finish"

# Run the simulation
$ns run
