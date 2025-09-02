# PLC-Traffic-Light
This project demonstrates a Traffic Light Control System implemented using PLC programming. The system cycles through Red, Green, and Yellow lights with predefined timers, simulating real-world traffic management at a road intersection.
(* PLC Traffic Light Control System *)

(* Inputs *)
(* I0.0 = System Start Button *)
(* I0.1 = Emergency Stop *)

(* Outputs *)
(* Q0.0 = Red Light *)
(* Q0.1 = Yellow Light *)
(* Q0.2 = Green Light *)

VAR
    State : INT := 0;   (* 0 = Red, 1 = Green, 2 = Yellow *)
    Timer : TON;
END_VAR

(* --- Emergency Stop --- *)
IF I0_1 THEN
    Q0_0 := FALSE;
    Q0_1 := FALSE;
    Q0_2 := FALSE;
    State := 0; (* Reset to Red *)
    RETURN;
END_IF;

(* --- Traffic Light Sequence --- *)
CASE State OF
    0:  (* Red Light ON for 10 sec *)
        Q0_0 := TRUE; Q0_1 := FALSE; Q0_2 := FALSE;
        Timer(IN:=TRUE, PT:=T#10s);
        IF Timer.Q THEN
            State := 1;
            Timer(IN:=FALSE);
        END_IF;

    1:  (* Green Light ON for 8 sec *)
        Q0_0 := FALSE; Q0_1 := FALSE; Q0_2 := TRUE;
        Timer(IN:=TRUE, PT:=T#8s);
        IF Timer.Q THEN
            State := 2;
            Timer(IN:=FALSE);
        END_IF;

    2:  (* Yellow Light ON for 3 sec *)
        Q0_0 := FALSE; Q0_1 := TRUE; Q0_2 := FALSE;
        Timer(IN:=TRUE, PT:=T#3s);
        IF Timer.Q THEN
            State := 0;
            Timer(IN:=FALSE);
        END_IF;
END_CASE;
