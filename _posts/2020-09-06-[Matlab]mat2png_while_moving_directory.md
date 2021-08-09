---
tags: matlab data-preprocessing mat2png stft
toc: True
---
```
%Title = mat2png while moving directory
%Root_Dir = ./data_only_mat
%Description = There is only .mat file in directory

f = dir %Get SC* 3~41
f_size = size(f)
for f_index=3:f_size(1)
    cd (f(f_index).name) %move SC*
    second_f = dir %Get SleepStage
    second_f_size = size(second_f)
    for second_f_index=3:second_f_size(1)
        cd (second_f(second_f_index).name) %move each sleep stage
        %% mat2png
        mat_file = dir
        mat_file_size = size(mat_file)
        for mat_file_index=3:mat_file_size(1)
            name = mat_file(mat_file_index).name %setdata
            [filename,fileformat] = strsplit(name,'.')
            load(name) 
            
            %% mat2stft
            [S,F,T] = stft(a)
            h = pcolor(T,F,abs(S))
            set(h,'EdgeColor','none');
            axis tight off
            
            %% stft2fig
            figname = strcat(filename(1),'.fig')
            savefig(figname{1})
            figs = openfig(figname{1})
            
            %% Erase blank
            style = hgexport('factorystyle')
            style.Bounds = 'Tight'
            hgexport(figs,'-Clipboard',style,'ApplyStyle',true)
            F = getframe(figs);
            
            %% fig2png
            pngname = strcat(filename(1),'.png')
            imwrite(F.cdata,pngname{1})
            
            close all
        end
        cd ../
    end
    cd ../
end
```
