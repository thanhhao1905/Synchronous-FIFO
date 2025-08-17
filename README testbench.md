````verilog
`timescale 1ns/1ps
module tb_fifo_sync_rst_n;
  parameter Depth =8 , Width =8;
  
  reg clk ,rst_n ,w_en ,r_en;
  reg [Width-1 :0] data_in;
  wire [Width-1 :0] data_out;
  wire full,empty;
  integer i;
  
  fifo_sync_rst_n #(Depth , Width) DUT (clk ,rst_n ,w_en ,r_en ,data_in ,data_out ,full ,empty);
  
  always #5 clk = ~clk;
  
  initial begin
    
    $monitor("$time=%0t,clk=%b ,rst_n=%b ,w_en=%b ,r_en=%b ,data_in=%b ,data_out=%b, full=%b ,empty=%b",$time ,clk ,rst_n ,w_en ,r_en ,data_in ,data_out ,full ,empty);
    
    clk =0;
    rst_n = 0;
    w_en =0;
    r_en =0;
    data_in =0;
    
    #10
    $display ("---- FIFO test Starting ----");
    
    #10 rst_n =1;
    #10;
    
    test_basic_write_read();
    #50;
    test_full_condition();
    #50;
    test_empty_condition();
    #50;
    test_wrap_around();
    #50
    random_test ();
    
    $display ("---- All test complete ----");
    #50;
    $finish;
  end
  
  // test_basic_write_read
  task test_basic_write_read ();
    $display ("---- Test Basic Write & Read ---- ");
    for(i=0; i<5; i=i+1) begin
      @(posedge clk);
      w_en <= 1;
      data_in <= i + 8'd15;
      @(posedge clk);
      w_en <= 0;
    end
    
    for(i=0;i<5;i=i+1) begin
      @(posedge clk);
      r_en <= 1;
      @(posedge clk);
      r_en <=0;
    end
    $display ("---- Finish Test Basic Write & Read ---- ");
  endtask
  
  
  // test_full_condition
  task test_full_condition ();
    $display ("---- Test Full Condition ---- ");
    for(i=0; i<Depth; i=i+1) begin
      @(posedge clk);
      w_en <= 1;
      data_in <= i + 8'd60;
      @(posedge clk);
      w_en <= 0;
    end
    
    for(i=0; i<4; i=i+1) begin
      @(posedge clk)
      w_en <= 1;
      data_in <= i + 8'd70;
      @(posedge clk)
      w_en <= 0;
    end
    
    for(i=0; i<12; i=i+1) begin
      @(posedge clk)
      r_en <= 1;
      @(posedge clk)
      r_en <= 0;
    end
    $display ("---- Finish Test Full Condition ---- ");
  endtask
  
  //test_empty_condition
  task test_empty_condition ();
    $display ("---- Test Empty Condition ----");
    rst_n <= 0;
    repeat(2) @(posedge clk);
    rst_n <= 1;
    
    for(i=0; i<Depth; i=i+1) begin
    @(posedge clk);
    r_en <= 1;
    @(posedge clk);
    r_en <= 0;
    end
    $display ("---- Finish Test Empty Condition ----");
  endtask
  
  //test_wrap_around
  task test_wrap_around ();
    $display ("---- Test Wrap Around ----");
    for(i=0; i<Depth; i=i+1) begin
      @(posedge clk);
      w_en <= 1;
      data_in <= i+8'd80;
      @(posedge clk);
      w_en <= 0;
    end
    
    for(i=0; i<4; i=i+1) begin
      @(posedge clk);
      r_en <= 1;
      @(posedge clk);
      r_en <=0;
    end
    
    for(i=0; i<4; i=i+1) begin
      @(posedge clk);
      w_en <= 1;
      data_in <= i+8'd88;
      @(posedge clk);
      w_en <= 0;
    end
    $display ("---- Finish Test Wrap Around ----");
  endtask
  
  //random test
  task random_test ();
    $display ("---- Random Test ----");
    integer n;
    rst_n <= 0;
    repeat(2) @(posedge clk);
    rst_n <= 1;
    n = $urandom % Depth;
    
    for(i=0; i< n ; i=i+1) begin
      @(posedge clk);
      w_en <= 1;
      data_in <= $urandom % (2**Width);
      @(posedge clk);
      w_en <= 0;
    end
    
    for(i=0; i<n ; i=i+1) begin
      @(posedge clk);
      r_en <= 1;
      @(posedge clk);
      r_en <=0;
    end
    
    $display ("---- Finish Random Test ----");
  endtask
  
  initial begin
    $dumpfile("dump.vcd");
    $dumpvars;
  end
endmodule
