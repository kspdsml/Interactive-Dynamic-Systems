function varargout = INK(varargin)

%% This is an Interface for capturing digital ink during sketching
%  wriiten for MAE 527 Intelligent CAD Interface course, Spring 2022 
%  Students are supposed to use this platform as a base for thire class
%  project ad HWs. Furthere improvement of this interface should be done by
%  students.
%
%  ET Esfahani
%  Department of Mechanical and Aerospace Engineering, 
%  University at Buffalo, State University of New York
%%

% INK M-file for INK.fig
%      INK, by itself, creates a new INK or raises the existing
%      singleton*.
%
%      H = INK returns the handle to a new INK or the handle to
%      the existing singleton*.
%
%      INK('CALLBACK',hObject,eventData,handles,...) calls the local
%      function named CALLBACK in INK.M with the given input arguments.
%
%      INK('Property','Value',...) creates a new INK or raises the
%      existing singleton*.  Starting from the left, property value pairs are
%      applied to the GUI before INK_OpeningFcn gets called.  An
%      unrecognized property name or invalid value makes property application
%      stop.  All inputs are passed to INK_OpeningFcn via varargin.
%
%      *See GUI Options on GUIDE's Tools menu.  Choose "GUI allows only one
%      instance to run (singleton)".
%
% See also: GUIDE, GUIDATA, GUIHANDLES

% Edit the above text to modify the response to help INK

% Last Modified by GUIDE v2.5 04-Feb-2022 11:14:03

% Begin initialization code - DO NOT EDIT
gui_Singleton = 1;
gui_State = struct('gui_Name',       mfilename, ...
    'gui_Singleton',  gui_Singleton, ...
    'gui_OpeningFcn', @INK_OpeningFcn, ...
    'gui_OutputFcn',  @INK_OutputFcn, ...
    'gui_LayoutFcn',  [] , ...
    'gui_Callback',   []);
if nargin && ischar(varargin{1})
    gui_State.gui_Callback = str2func(varargin{1});
end

if nargout
    [varargout{1:nargout}] = gui_mainfcn(gui_State, varargin{:});
else
    gui_mainfcn(gui_State, varargin{:});
end
% End initialization code - DO NOT EDIT


% --- Executes just before INK is made visible.
function INK_OpeningFcn(hObject, eventdata, handles, varargin)
% This function has no output args, see OutputFcn.
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
% varargin   command line arguments to INK (see VARARGIN)

% Choose default command line output for INK
handles.output = hObject;

% Update handles structure
guidata(hObject, handles);

% UIWAIT makes INK wait for user response (see UIRESUME)
% uiwait(handles.figure1);

global ghandles;
global points;
global pID;
global drawing;
global sketchID;

ghandles = handles;
tic
axis([0 1 0 1])
hold on
set(gca,'XTick',[],'Ytick',[],'Box','on')
set(ghandles.pushbutton1,'Enable','off')
set(ghandles.pushbutton2,'Enable','off')
set(gcf,'WindowButtonDownFcn',@mouseDown,'WindowButtonMotionFcn',@mouseMoving,'WindowButtonUpFcn',@mouseUp)

points = zeros(1000,3);
pID = 0;
drawing = 0;
sketchID = 0;
% --- Outputs from this function are returned to the command line.
function varargout = INK_OutputFcn(hObject, eventdata, handles)
% varargout  cell array for returning output args (see VARARGOUT);
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Get default command line output from handles structure
varargout{1} = handles.output;


% --- Executes on button press in pushbutton1.
function pushbutton1_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global points;
global pID;
global sketchID;

cla
points = zeros(1000,3);
pID = 0;


% --- Executes on button press in pushbutton2.
function pushbutton2_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton2 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global points;
global pID;
global sketchID;

if pID < 1000
    points(pID+1:end,:)=[];
end

% CLASSIFY 
load("training_data.mat",'training_data')
resampled_points=resample(points);
resampled_templates = cellfun(@resample,training_data,'UniformOutput',false);
rotated_points = rotate_to_zero(resampled_points);
rotated_templates = cellfun(@rotate_to_zero, resampled_templates, 'UniformOutput',false);
scaled_points = scale_to_square(rotated_points);
scaled_templates = cellfun(@scale_to_square,rotated_templates,'UniformOutput',false);

translated_points = translate_to_origin(scaled_points);
translated_templates = cellfun(@translate_to_origin,scaled_templates,'UniformOutput',false);

[T_prime, score] = recognize(translated_points, translated_templates)

function mouseDown(hObject, eventdata, handles)

global ghandles;
global points;
global pID;
global drawing;

cp = get(gca,'CurrentPoint');

if cp(1,1)<1 && cp(1,1)>0 && cp(1,2)<1 && cp(1,2)>0
    drawing = 1;
end


function mouseMoving(hObject, eventdata, handles)
global ghandles;
global points;
global pID;
global drawing;

if drawing
    t = toc;
    cp = get(gca,'CurrentPoint');
    pID = pID + 1;
    points(pID,1) = cp(1,1);
    points(pID,2) = cp(1,2);
    points(pID,3) = t;
    plot(cp(1,1),cp(1,2),'o')

end


function mouseUp(hObject, eventdata, handles)


global ghandles;
global points;
global pID;
global drawing;
global sketchID;

if drawing
    drawing = 0;
    sketchID = sketchID+1;
end
set(ghandles.pushbutton1,'Enable','on')
set(ghandles.pushbutton2,'Enable','on')
