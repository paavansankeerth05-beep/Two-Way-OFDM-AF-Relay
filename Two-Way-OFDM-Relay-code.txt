%% 1. System Parameters
N = 64;             
cp_len = 16;       
snr_db = 0:2:20;    
num_iters = 500;    
target_rate = 1.0;  

% Initialize arrays
[ber_VG, ber_FG, out_VG, out_FG] = deal(zeros(size(snr_db)));

%% 2. Multi-Time Simulation Loop
for i = 1:length(snr_db)
    snr_lin = 10^(snr_db(i)/10);
    err_VG = 0; err_FG = 0; out_cnt_VG = 0; out_cnt_FG = 0;
    
    for j = 1:num_iters
       
        dataB = randi([0 3], N, 1);
        symA = pskmod(randi([0 3], N, 1), 4, pi/4);
        symB = pskmod(dataB, 4, pi/4);
        
       
        tA = ifft(symA, N); 
        txA = [tA(end-cp_len+1:end); tA];
        tB = ifft(symB, N); 
        txB = [tB(end-cp_len+1:end); tB];
        
        
        hA = (randn + 1i*randn)/sqrt(2); 
        hB = (randn + 1i*randn)/sqrt(2);
        G_VG = sqrt(1 / (abs(hA)^2 + abs(hB)^2 + 1/snr_lin)); % Variable
        G_FG = 0.5; % Fixed Gain
        
       
        n_r = (randn(size(txA)) + 1i*randn(size(txA)))/sqrt(2*snr_lin);
        y_relay = hA*txA + hB*txB + n_r;
        n_a = (randn(size(txA)) + 1i*randn(size(txA)))/sqrt(2*snr_lin);
        
        
        yA_VG = hA*(G_VG * y_relay) + n_a;
        rem_self_VG = yA_VG - (hA*G_VG*hA*txA); % Self-interference removal
        rx_time_VG = rem_self_VG(cp_len+1:end);
        rx_freq_VG = fft(rx_time_VG, N);
        eq_symB_VG = rx_freq_VG ./ (hA*G_VG*hB);
        rxB_VG = pskdemod(eq_symB_VG, 4, pi/4);
        err_VG = err_VG + sum(dataB ~= rxB_VG);
        
        snr_eff_VG = (abs(hA)^2*abs(hB)^2*snr_lin^2)/((abs(hA)^2+abs(hB)^2)*snr_lin + 1);
        if (N/(N+cp_len))*log2(1 + snr_eff_VG) < target_rate, out_cnt_VG = out_cnt_VG + 1;
        end

       
        yA_FG = hA*(G_FG * y_relay) + n_a;
        rem_self_FG = yA_FG - (hA*G_FG*hA*txA);
        rx_time_FG = rem_self_FG(cp_len+1:end);
        rx_freq_FG = fft(rx_time_FG, N);
        eq_symB_FG = rx_freq_FG ./ (hA*G_FG*hB);
        rxB_FG = pskdemod(eq_symB_FG, 4, pi/4);
        err_FG = err_FG + sum(dataB ~= rxB_FG);
        
        snr_eff_FG = (abs(hA)^2*abs(hB)^2*G_FG^2*snr_lin)/(abs(hA)^2*G_FG^2 + 1);
        if (N/(N+cp_len))*log2(1 + snr_eff_FG) < target_rate, out_cnt_FG = out_cnt_FG + 1; end
        
        
        if i == length(snr_db) && j == 1
             const_VG = eq_symB_VG;
             const_FG = eq_symB_FG;
        end
    end
    ber_VG(i) = err_VG/(N*num_iters); ber_FG(i) = err_FG/(N*num_iters);
    out_VG(i) = out_cnt_VG/num_iters; out_FG(i) = out_cnt_FG/num_iters;
end

%% 3. Visualization

figure('Name', 'Performance Comparison');
subplot(2,1,1);
semilogy(snr_db, ber_FG, 'r--o', snr_db, ber_VG, 'b-s', 'LineWidth', 1.5);
legend('Fixed Gain','Variable Gain'); ylabel('BER'); grid on; title('BER vs SNR');

subplot(2,1,2);
semilogy(snr_db, out_FG, 'r--o', snr_db, out_VG, 'b-s', 'LineWidth', 1.5);
legend('Fixed Gain','Variable Gain'); xlabel('SNR (dB)'); ylabel('Outage Probability'); grid on; title('Outage Probability vs SNR');

figure('Name', 'Constellation Comparison');
subplot(1,2,1); plot(const_FG, 'r.'); title('Fixed Gain (FG)'); axis([-2 2 -2 2]); grid on;
subplot(1,2,2); plot(const_VG, 'b.'); title('Variable Gain (VG)'); axis([-2 2 -2 2]); grid on;