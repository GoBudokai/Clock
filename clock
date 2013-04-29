#include <iostream>
#include <cstring>
#include <cstdio>
#include <cstdlib>
#include <math.h>

//////////////////////////////////////////////////////////////////////////////////////////////////////////

using std::string;
using std::cout;
using std::endl;

//////////////////////////////////////////////////////////////////////////////////////////////////////////

const float PI = 3.1415;
const char* AM = "AM";
const char* PM = "PM";
const char* RAD = "rad";
const char* DEG = "deg";
const char* DMS = "dms";
const char* MECH = "mech";
const char* QUARTZ = "quartz";
enum { EX_ARGNUM_ERR = 0,  EX_TIMEFORM_ERR, EX_OUTFORM_ERR, EX_CLOCKFORM_ERR, EX_MEMOERR};

//////////////////////////////////////////////////////////////////////////////////////////////////////////

class Excep
{
public:
    Excep(int excnum);
    inline string getMessage() const
    {return exc_word;}
private:
    string exc_word;
    int err_num;
};

class Time
{
public:
    inline int getMin() const
    {return min;}
    inline int getHour() const
    {return hour;}
private:
    int hour;
    int min;
protected:
    void checkSetTime(char *arg);
};

class Clock:public Time
{
public:
    Clock(): c_type(false){}
    void setClock(int argc, char **argv);
    string getAngle() const;
    string getAngleDeg() const;
    string getAngleRad() const;
    string getAngleDms() const;
private:
    bool c_type;
    char out_type[4];
    int deg;
    int d_min;
    double rad;
    void calcAngle();
    void checkClock(char **argv);
};

//////////////////////////////////////////////////////////////////////////////////////////////////////////

Excep::Excep(int excnum)
{
    err_num = excnum;
    switch(excnum)
    {
        case 0: exc_word = "Error number of arguments";
                break;
        case 1: exc_word = "Time format error";
                break;
        case 2: exc_word = "Output format error";
                break;
        case 3: exc_word = "Clock format error";
                break;
        case 4: exc_word = "Memory error";
                break;
    }
}

void Time::checkSetTime(char *arg)
{
    if(strlen(arg) != 5 && strlen(arg) != 8)
        throw Excep(EX_TIMEFORM_ERR);
    for(int i = 0; i < 5; i++)
        if((arg[i] < '0' || arg[i] > '9') && i != 2)
            throw Excep(EX_TIMEFORM_ERR);
    hour = ((arg[0] - '0') * 10 + arg[1] - '0');
    min = ((arg[3] - '0') * 10 + arg[4] - '0');
    if(arg[2] != ':')
        throw Excep(EX_TIMEFORM_ERR);
    if(min > 59 || min < 0)
        throw Excep(EX_TIMEFORM_ERR);
    if(hour < 0 || hour > 23)
        throw Excep(EX_TIMEFORM_ERR);
    if(strlen(arg) == 8)
    {
        if(arg[5] != ' ')
            throw Excep(EX_TIMEFORM_ERR);
        char temp[3];
        strcpy(temp, arg + 6);
        if(strcmp(temp, AM) && strcmp(temp, PM))
            throw Excep(EX_TIMEFORM_ERR);
        if(hour > 11)
            throw Excep(EX_TIMEFORM_ERR);
    }
    hour = hour % 12;
}

void Clock::checkClock(char **argv)
{
    if(strcmp(argv[3], QUARTZ) && strcmp(argv[3], MECH))
        throw Excep(EX_CLOCKFORM_ERR);
    if(!strcmp(argv[3], MECH))
        c_type = true;
    if(strcmp(argv[2], RAD) && strcmp(argv[2], DEG) && strcmp(argv[2], DMS))                                         //check the output format
        throw Excep(EX_OUTFORM_ERR);
    strcpy(out_type, argv[2]);
}

void Clock::calcAngle()
{
    double temp_deg;
    if(getMin() / 5 >= getHour())
        temp_deg =(getMin() * 6 - (getHour() * 30 + c_type * static_cast<double>(getMin()) / 2));
    else
        temp_deg =((getHour() * 30 + c_type * static_cast<double>(getMin()) / 2) - getMin() * 6);
    if(temp_deg > 180)
        temp_deg = 360 - temp_deg;
    deg = static_cast<int>(temp_deg);
    rad = (2 * PI / 360) * temp_deg;
    d_min = static_cast<int>((temp_deg - static_cast<int>(temp_deg)) * 60);
}

void Clock::setClock(int argc, char **argv)
{
    if(argc != 4)
        throw Excep(EX_ARGNUM_ERR);
    checkSetTime(argv[1]);
    checkClock(argv);
    calcAngle();
}

string Clock::getAngleDeg() const
{
    if(deg > 99)
    {
        char str[4];
        if(str == NULL)
            throw Excep(EX_MEMOERR);
        str[0] = deg / 100 + '0';
        str[1] = (deg % 100) / 10 + '0';
        str[2] = deg % 10 + '0';
        str[3] = '\0';
        return string(str);
    }
    char str[3];
    if(str == NULL)
        throw Excep(EX_MEMOERR);
    str[0] = deg / 10 + '0';
    str[1] = deg % 10 + '0';
    str[2] = '\0';
    return string(str);
}
string Clock::getAngleRad() const
{
    int temp = rad * 10000;
    char str[7];
    str[0] = static_cast<int>(rad) + '0';
    str[1] = '.';
    int del_1 = 10000;
    int del_2 = 1;
    temp %= del_1;
    for(int i = 2; i < 5; i++, temp %= del_1)
    {
        del_1 /= 10;
        del_2 *= 10;
        str[i] = (temp / del_1) % del_2 + '0';
    }
    str[5] = temp % 10 + '0';
    str[6] = '\0';
    return string(str);

}
string Clock::getAngleDms() const
{
    char str[8];
    string temp = getAngleDeg();
    str[0] = '.';
    str[1] = d_min / 10 + '0';
    str[2] = d_min % 10 + '0';
    strcpy(str + 3, "'00\"\0");
    temp += str;
    return temp;
}
string Clock::getAngle() const
{
    if(!strcmp(out_type, RAD))
        return getAngleRad();
    else if(!strcmp(out_type, DEG))
        return getAngleDeg();
    else if(!strcmp(out_type, DMS))
        return getAngleDms();
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////
int main(int argc, char *argv[])
{
    try
    {
        Clock clock;
        clock.setClock(argc, argv);
        cout << clock.getAngle();
    }
    catch(Excep err)
    {cout << err.getMessage();}
    catch(...)
    {cout << "err";}

    return 0;
}
