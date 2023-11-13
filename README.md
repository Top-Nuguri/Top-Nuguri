`timescale 1ns / 1ps

module watch2_hour(clk, rst, key, smotor,
 seg_data, seg_com);
 
 input clk, rst;
 input [3:0] key;
 output [7:0] seg_data, seg_com;
 output [3:0] smotor;
 
 reg [9:0] h_cnt;
 reg [13:0] smt;
 reg [3:0] h_ten, h_one, m_ten, m_one, s_ten, s_one;
 
 wire [7:0] seg_h_ten, seg_h_one;
 wire [7:0] seg_m_ten, seg_m_one;
 wire [7:0] seg_s_ten, seg_s_one;
 
 reg [2:0] s_cnt;
 reg [7:0] seg_data, seg_com;
 reg [3:0] smotor;
 
 always @(posedge clk)
 if (rst) s_cnt <= 0;
 else if (s_cnt >= 5)
 s_cnt = 0;
 else s_cnt <= s_cnt + 1;
 
 always @(posedge clk)
 if (rst) seg_com = 8'b1111_1111;
 else
 case (s_cnt)
 3'd0: seg_com = 8'b1101_1111;
 3'd1: seg_com = 8'b1110_1111;
 3'd2: seg_com = 8'b1111_0111;
 3'd3: seg_com = 8'b1111_1011;
 3'd4: seg_com = 8'b1111_1101;
 3'd5: seg_com = 8'b1111_1110;
 endcase
 
 always @(posedge clk)
 if (rst) seg_data = 8'b0000_0000;
 else
 case (s_cnt)
 3'd0: seg_data = seg_h_ten;
 3'd1: seg_data = seg_h_one;
 3'd2: seg_data = seg_m_ten;
 3'd3: seg_data = seg_m_one;
 3'd4: seg_data = seg_s_ten;
 3'd5: seg_data = seg_s_one;
 endcase
 
 always @ (key)
  case (key)
   4'b1000 : h_one = h_one +1;
   4'b0100 : h_one = h_one -1;
   4'b0010 : m_one = m_one +1;
   4'b0001 : m_one = m_one -1;
  endcase
 
 seg_decoder u2(h_ten, seg_h_ten);
 seg_decoder u3(h_one, seg_h_one);
 seg_decoder u4(m_ten, seg_m_ten);
 seg_decoder u5(m_one, seg_m_one);
 seg_decoder u6(s_ten, seg_s_ten);
 seg_decoder u7(s_one, seg_s_one);

 always @(posedge rst, posedge clk)
 if (rst) smt <= 0;
 else if (smt == 14999) smt <= 0;
 else smt <= smt + 1;
 
 always @(posedge rst, posedge clk)
 if (rst) h_cnt <= 0;
 else if (h_cnt == 999) h_cnt <= 0;
 else h_cnt <= h_cnt + 1;
 
 always @(posedge rst, posedge clk)
 if (rst) s_one <= 0;
 else if (h_cnt == 999)
 s_one <= (s_one>=9) ? 0 : s_one + 1;
 
 always @(posedge rst, posedge clk)
 if (rst) s_ten <= 0;
 else if ((h_cnt==999) && (s_one==9))
 s_ten <= (s_ten>=5) ? 0 : s_ten + 1;
 
 always @(posedge rst, posedge clk)
 if (rst) m_one <= 0;
 else if((h_cnt == 999) &&
 (s_one==9) && (s_ten==5))
 m_one <= (m_one>=9) ? 0 : m_one + 1;
 
 always @(posedge rst, posedge clk)
 if (rst) m_ten <= 0;
 else if ((h_cnt==999) && (s_one==9)
 && (s_ten==5) && (m_one==9))
 m_ten <= (m_ten>=5) ? 0 : m_ten + 1 ;

 always @(posedge rst, posedge clk)
 if (rst) h_one <= 0;
 else if((h_cnt==999) && (s_one ==9) && 
 (s_ten==5) && (m_one==9) && (m_ten ==5))
 h_one <= (h_one>=5) ? 0 : h_one + 1 ;

always @(posedge rst, posedge clk)
 if (rst) h_ten <= 0;
 else if((h_cnt==999) && (s_one ==9) && 
 (s_ten==5) && (m_one==9) && (m_ten ==5) && (h_one==9))
 h_ten <= (h_ten>=5) ? 0 : h_ten + 1 ;

always @(posedge rst, posedge clk)
 if (rst) begin 
 smt <= 0;
 smotor = 4'b1001;
 end
  else if (smt==14999) begin
   smotor[0] <= smotor[3]; // [0]에 [3] 값을 입력해라
   smotor[1] <= smotor[0]; // [1]에 [0] 값을 입력해라
   smotor[2] <= smotor[1]; // [2]에 [1] 값을 입력해라
   smotor[3] <= smotor[2]; // [3]에 [2] 값을 입력해라
 end
 
endmodule
