````verilog
module fifo_sync_rst_n #(parameter Depth =8 , Width =8) (input wire clk ,rst_n ,w_en ,r_en,
                                                         input wire [Width-1 :0] data_in,
                                                         output reg [Width-1 :0] data_out,
                                                         output wire full,empty);
  reg [$clog2(Depth) :0] w_ptr;
  reg [$clog2(Depth) :0] r_ptr;
  reg [Width-1 :0] fifo [0: Depth-1];
  
  assign empty = (w_ptr == r_ptr);
  assign full = (w_ptr[$clog2(Depth)-1 :0] == r_ptr[$clog2(Depth)-1 :0]) 
    && (w_ptr[$clog2(Depth)] !== r_ptr[$clog2(Depth)]);
  
  always @ (posedge clk or negedge rst_n)
    
    if(!rst_n) begin
      r_ptr <= 0;
      w_ptr <= 0;
      data_out <=0;
    end

else if (w_en && !full) begin
      fifo [w_ptr[$clog2(Depth)-1 :0]] <= data_in;
      w_ptr <= w_ptr +1;
    end

else if (r_en && !empty) begin
      data_out <= fifo [r_ptr[$clog2(Depth)-1 :0]];
      r_ptr = r_ptr +1;
    end
endmodule
