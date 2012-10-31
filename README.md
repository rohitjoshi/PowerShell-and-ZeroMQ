PowershellZeroMQ
================

Accessing ZeroMQ from Windows PowerShell: 

Window PowerShell support loading and accessing functionality from any CLR assemblies. 

To load the assembly, you need to use LoadFile function.
[System.Reflection.Assembly]::LoadFile("C:\zmqclr\bin\clrzmq.dll");.


To create the ZeroMQ Context which is a static function,
 [ZeroMQ.ZmqContext]::Create()


To create a socket (push) from Zmq Context
([ZeroMQ.ZmqSocket]$zmqCtx.CreateSocket([ZeroMQ.SocketType]::PUSH));


And now connect to the central logging server on port 10000
zmqSocket.Connect("tcp://localhost:10000");


Once Socket is connected, you can send messages using send function.
zmqSocket.Send($data, $data.Length, [ZeroMQ.SocketFlags]::None)

Below is the script.

<pre>
#=======================================================================
# Purpose: Send logs to logging server using ZeroMQ
# Author: Rohit Joshi
# Date: 10/31/2012
#=======================================================================
#Global variables
$global:zmqSocket=$null
$enc = [system.Text.Encoding]::Unicode

#=======================================================================
# Function: Init ZeroMQ
# Arguments:
#=======================================================================
# Purpose: Initialize the zeromq
#=======================================================================
Function InitZeroMQ()
{
  [System.Reflection.Assembly]::LoadFile("C:\zmqclr\bin\clrzmq.dll");
  $zmqCtx= [ZeroMQ.ZmqContext]::Create()
  $global:zmqSocket =([ZeroMQ.ZmqSocket]$zmqCtx.CreateSocket([ZeroMQ.SocketType]::PUSH));
  $global:zmqSocket.SendHighWatermark = 5000;
  $global:zmqSocket.Connect("tcp://localhost:10000");
}

#=======================================================================
# Function: SendMessage
# Arguments: string 
#=======================================================================
# Purpose:Send Message
#=======================================================================
Function SendMessage($msg)
{
 $data=$enc.GetBytes($msg) 
 $global:zmqSocket.Send($data, $data.Length, [ZeroMQ.SocketFlags]::None)
}

InitZeroMQ();
SendMessage("Sending data from PowerShell script");
</pre>