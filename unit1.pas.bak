unit Unit1;

{$mode delphi}{$H+}

interface

uses
  Classes, SysUtils, FileUtil, Forms, Controls, Graphics, Dialogs, ExtCtrls,
  Grids, StdCtrls, ColorBox, Spin, Buttons, ComCtrls;

const
MaxGrid = 100; //Максимальный размер поля

type

  { TMainForm }

  TMainForm = class(TForm)
    Label5: TLabel;
    StartBtn: TBitBtn; //Кнопка старт/стоп, запускающая игру
    ClearBtn: TBitBtn; //Кнопка, очищающая игровое поле
    NextBtn: TBitBtn;
    CellColorBox: TColorBox; //Цвет, которым будет закрашиваться поле
    LifeGrid: TDrawGrid; //Игровое поле
    GroupBox1: TGroupBox;
    GroupBox2: TGroupBox;
    Label1: TLabel;
    Label2: TLabel;
    Label3: TLabel;
    Label4: TLabel;
    Panel1: TPanel;
    Panel2: TPanel;
    Panel3: TPanel;
    WSizeEdit: TSpinEdit; //Размер игрового поля по горизонтали
    HSizeEdit: TSpinEdit; //Размер игрового поля по вертикали
    Splitter1: TSplitter;
    StatusBar: TStatusBar;
    LifeTimer: TTimer;
    TrackBar1: TTrackBar;

    procedure CellColorBoxChange(Sender: TObject);
    procedure ClearBtnClick(Sender: TObject);
    procedure FormCreate(Sender: TObject);
    procedure HSizeEditChange(Sender: TObject);
    //Обработка события на изменение значения в WSizeEdit
    procedure LifeGridDrawCell(Sender: TObject; aCol, aRow: Integer;
      aRect: TRect; aState: TGridDrawState);  //Кнопка пошагового прохода
//Обработка события на изменение значения в HSizeEdit
    procedure LifeGridMouseDown(Sender: TObject; Button: TMouseButton;
      Shift: TShiftState; X, Y: Integer);
    procedure LifeGridMouseMove(Sender: TObject; Shift: TShiftState; X,
      Y: Integer);
    procedure LifeGridMouseUp(Sender: TObject; Button: TMouseButton;
      Shift: TShiftState; X, Y: Integer);
    procedure NextBtnClick(Sender: TObject);
    procedure StartBtnClick(Sender: TObject);
    procedure LifeTimerTimer(Sender: TObject);
    procedure TrackBar1Change(Sender: TObject);
    procedure WSizeEditChange(Sender: TObject);
  private
    // Матрица, описывающая игровое поле: 0 — мертвая клетка, 1 — живая
GameField: Array [0..MaxGrid+1, 0..MaxGrid+1] of 0..1;
MouseDownFlag: Boolean; //Флаг, говорящий о том,
//нажата ли клавиша мыши
OldC, OldR: Integer; // переменные, необходимые для вычисления клетки
// по координатам мыши
GenCount: Integer; //Счетчик популяций
function CalcNeib(i, j: Integer): Integer; //Посчитать соседей клетки
//с координатами i и j
procedure GetNextGen; // Получить следующую популяцию.
    { private declarations }
  public
    { public declarations }
  end;

var
  MainForm: TMainForm;

implementation

{$R *.lfm}

{ TMainForm }

procedure TMainForm.TrackBar1Change(Sender: TObject);
begin
  // Установка таймера
  LifeTimer.Interval := TrackBar1.Position;
end;

// Изменение количества строк игрового поля
procedure TMainForm.WSizeEditChange(Sender: TObject);
begin
  //Для установки количества столбцов игрового поля
  //берем значение из WSizeEdit
  LifeGrid.RowCount := WSizeEdit.Value;
end;

procedure TMainForm.LifeTimerTimer(Sender: TObject);
  // Создание мультипликации из жизни
begin
  // Рассчитаем следующее поколение
  GetNextGen;
  // Обновим поле
  LifeGrid.Refresh;
  // Увеличим счетчик поколений и обновим статусбар
  Inc(GenCount);
  StatusBar.SimpleText := 'Поколения: ' + IntToStr(GenCount);
end;

procedure TMainForm.StartBtnClick(Sender: TObject);
  // Запуск и остановка таймера
begin
  if StartBtn.Caption = 'Старт' then
  begin
    StartBtn.Caption := 'Стоп';
    LifeTimer.Enabled := true;
    NextBtn.Enabled := False;
  end
  else
  begin
    StartBtn.Caption := 'Старт';
    LifeTimer.Enabled := false;
    NextBtn.Enabled := true;
  end;
end;

procedure TMainForm.NextBtnClick(Sender: TObject);
  //Обработаем нажатие на кнопку следующее поколение
begin
  // Рассчитаем следующее поколение
  GetNextGen;
  // Обновим поле
  LifeGrid.Refresh;
  // Увеличим счетчик поколений и обновим StatusBar
  Inc(GenCount);
  StatusBar.SimpleText := 'Поколения: ' + IntToStr(GenCount);
end;

procedure TMainForm.FormCreate(Sender: TObject);
  var
    i, j: Integer;
begin
  //Очистим игровое поле
  //Очищаем матрицу игрового поля
  for i := 1 to MaxGrid do
  for j := 1 to MaxGrid do GameField[i,j] := 0;
  MouseDownFlag := false;
  GenCount := 0; //Обнулим счетчик поколений
  StatusBar.SimpleText := 'Поколения: 0';
end;

// Изменение количества столбцов игрового поля
procedure TMainForm.HSizeEditChange(Sender: TObject);
begin
  //Для установки количества строк игрового поля
  //берем значение из HSizeEdit
  LifeGrid.ColCount := HSizeEdit.Value;
end;

//Перерисовка игрового поля в соответствии с матрицей
procedure TMainForm.LifeGridDrawCell(Sender: TObject; aCol, aRow: Integer;
  aRect: TRect; aState: TGridDrawState);
  var
    cl: TColor;
begin
  //При перерисовке LifeGrid'а смотрим на значение элемента
  //матрицы GameField с координатами ACol, ARow и если там 1, то
  //закрашиваем ее в цвет CellColorBox, в противном случае —  в цвет clWindow
  if GameField[ARow+1, ACol+1] = 1 then cl := CellColorBox.Selected
  else cl := clWindow;
  with LifeGrid.Canvas do
  begin
    Brush.Color := cl;
    Brush.Style := bsSolid;
    Pen.Color := cl;
    FillRect(aRect);
  end;
end;

procedure TMainForm.CellColorBoxChange(Sender: TObject);
begin
  //Перерисуем игровое поле при изменении цвета
  LifeGrid.Refresh;
end;


// Обработка очистки игрового поля
procedure TMainForm.ClearBtnClick(Sender: TObject);
  var
    i, j: Integer;
begin
  //Очищаем матрицу игрового поля
  for i := 1 to MaxGrid do
  for j := 1 to MaxGrid do GameField[i,j] := 0;
  //Обновляем визуальное игровое поле LifeGrid
  LifeGrid.Refresh;
  GenCount := 0; //Обнулим счетчик поколений
  StatusBar.SimpleText := 'Поколения: 0';
end;

procedure TMainForm.LifeGridMouseDown(Sender: TObject; Button: TMouseButton;
  Shift: TShiftState; X, Y: Integer);
begin
  //устанавливаем флаг нажатой кнопки мыши в истину
  MouseDownFlag := true;
  OldC := -1;
  OldR := -1;
end;

procedure TMainForm.LifeGridMouseMove(Sender: TObject; Shift: TShiftState; X,
  Y: Integer);
  var
  NewC, NewR: Integer;
begin
  // Обрабатываем движение мыши, если была нажата клавиша
  if MouseDownFlag then
  begin
    //Определяем клетку, по которой был клик, и для этой клетки
    //меняем значение в GameField на противоположное
    LifeGrid.MouseToCell(X, Y, NewC, NewR);
    // Изменяем ситуацию на поле, если мы попали в клетку, отличную
    // от предыдущей, поскольку мы можем двигать мышью медленно
    if (NewR<>OldR) Or (NewC<>OldC) then
    begin
      GameField[NewR+1, NewC+1] := (GameField[NewR+1, NewC+1] + 1) mod 2;
      LifeGrid.Refresh;
      OldC := NewC;
      OldR := NewR;
    end;
  end;
end;

procedure TMainForm.LifeGridMouseUp(Sender: TObject; Button: TMouseButton;
  Shift: TShiftState; X, Y: Integer);
begin
  //устанавливаем флаг нажатой кнопки мыши в ложь
  MouseDownFlag := false;
end;

function TMainForm.CalcNeib(i: Integer; j: Integer): Integer;
  // Считаем соседей клетки с учетом того, что поверхность моделирует тор
  var
  left, top, right, bottom: Integer;
begin
  if (j<>1) then left := j - 1
  else left := HSizeEdit.Value;
  if (j<>HSizeEdit.Value) then right := j + 1
  else right := 1;
  if (i<>1) then top := i - 1
  else top := WSizeEdit.Value;
  if (i<>WSizeEdit.Value) then bottom := i + 1
  else bottom := 1;
  Result := GameField[top, left] + GameField[top, j] +
  GameField[top, right] +
  GameField[i, left] + GameField[i, right] +
  GameField[bottom, left] + GameField[bottom, j] +
  GameField[bottom, right];
end;

procedure TMainForm.GetNextGen;
  // Проходим по матрице GameField и для каждой клетки вычисляем
  // новое значение по правилам игры
  var
  s, i, j: Integer;
  NewGameField: Array [0..MaxGrid+1, 0..MaxGrid+1] of 0..1;
begin
  // сформируем новое игровое поле
  for i := 1 to WSizeEdit.Value do
  for j := 1 to HSizeEdit.Value do
  begin
    s := CalcNeib(i, j); //Посчитали соседей
    // Если у пустой клетки 3 соседа, то в ней "зарождается жизнь"
    if (GameField[i, j] = 0) And (s = 3) then NewGameField[i, j] := 1
    else NewGameField[i, j] := 0;
    if (GameField[i, j] = 1) then
      begin
        if (s = 2) or (s = 3) then NewGameField[i, j] := 1
        else NewGameField[i, j] := 0;
      end;
  end;
  // перенесем новое игровое поле в старое
  for i := 1 to WSizeEdit.Value do
  for j := 1 to HSizeEdit.Value do GameField[i, j] := NewGameField[i, j];
end;

end.

