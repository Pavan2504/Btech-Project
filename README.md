clc;
clear all;
close  all;
a=imread('doc.bmp');
s=rgb2gray(a);
z=imresize(s,[256 256]);
N=256;
sigma = 0.1*255;  
noise = randn(N) * sigma;
zz =double(z)+ noise;
[cA(:,:,1) cA(:,:,2) cA(:,:,3) cA(:,:,4)]=dwt2(z,'haar');
fun1=@fft2;
fun2=@ifft;
fun3=@fft;
fun4=@ifft2;
for j=1:4
    x(:,:,j)=blkproc(cA(:,:,j),[8 8],fun1);
    y(:,:,j)=blkproc(x(:,:,j),[8 8],fun2);
    y1=y(:,:,j);
%     figure,
%     imshow(y1);
    y2=y1(:)';
    [cA1(:,:,j) cD1(:,:,j)]=dwt(y2,'haar');
%      figure,
%      imshow(cD1(:,:,j));
    re_y2=idwt(cA1(:,:,j),cD1(:,:,j),'haar');
    for i=1:128
        re_y1(1:128,i)=re_y2(((i-1)*128+1:i*128));
    end
    re_y(:,:,j)=re_y1;
    re_x(:,:,j)=blkproc(re_y(:,:,j),[8 8],fun3);
    re_cA1(:,:,j)=blkproc(re_x(:,:,j),[8  8],fun4);
end
re_c=idwt2(re_cA1(:,:,1),re_cA1(:,:,2),re_cA1(:,:,3),re_cA1(:,:,4),'haar');
se = strel('square',1);
re_ccc=imopen(re_c,se);
re_cccc=imclose(re_ccc,se);
figure,
subplot(2,2,1);imshow(zz,[]);title('Input Image');
subplot(2,2,2);imshow(re_c,[]);title('Reconstructed Image');
subplot(2,2,3);imshow(re_cccc,[]);title('Enhanced Image');
z1=double(z);
re_cc=double(re_c);
cl_cc=double(re_cccc);
mse2= sum((z1(:)-cl_cc(:)).^2) / prod(size(z1));
psnr2= 10*log10(255*255/mse2);
disp('PSNR of Enhanced Image');
disp(psnr2);

