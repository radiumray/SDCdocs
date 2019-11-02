# 临时代码

pidGUI：

```py


#!/usr/bin/python3
# -*- coding: utf-8 -*-
 
import sys
from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import (QWidget, QLCDNumber, QSlider,
                            QVBoxLayout, QApplication, QHBoxLayout,
                            QPushButton, QLabel, QGroupBox,
                            QDoubleSpinBox, )
from matplotlib.backends.backend_qt4agg import FigureCanvasQTAgg as FigureCanvas
from matplotlib.figure import Figure
from matplotlib.animation import FuncAnimation
import numpy as np
import PID
import time

isStatic=False


class MyMplCanvas(FigureCanvas):
    """Ultimately, this is a QWidget (as well as a FigureCanvasAgg, etc.)."""
    def __init__(self, parent=None, width=5, height=10, dpi=100):
        fig = Figure(figsize=(width, height), dpi=dpi)
        self.axes = fig.add_subplot(111)
        # We want the axes cleared every time plot() is called
        # self.axes.hold(False)

        self.axes.set_xlim(0, 20)
        self.axes.set_ylim(-2, 10)

        self.compute_initial_figure()

        FigureCanvas.__init__(self, fig)
        self.setParent(parent)

    def compute_initial_figure(self):
        pass

class Example(QWidget):

    def __init__(self, pid):
        super().__init__()
        self.pid=pid
        self.initUI()

    def initUI(self):

        self.labelP = QLabel('P:', self)
        self.labelP.setAlignment(Qt.AlignRight)
        self.labelI = QLabel('I:', self)
        self.labelI.setAlignment(Qt.AlignRight)
        self.labelD = QLabel('D:', self)
        self.labelD.setAlignment(Qt.AlignRight)


        self.spinBoxP = QDoubleSpinBox(self)
        self.spinBoxP.setValue(self.pid.getKp())
        self.spinBoxP.setSingleStep(0.01)
        self.spinBoxP.setMinimum(.01)
        self.spinBoxP.setMaximum(19.99)

        self.spinBoxI = QDoubleSpinBox(self)
        self.spinBoxI.setValue(self.pid.getKi())
        self.spinBoxI.setSingleStep(0.01)
        self.spinBoxI.setMinimum(.01)
        self.spinBoxI.setMaximum(19.99)

        self.spinBoxD = QDoubleSpinBox(self)
        self.spinBoxD.setDecimals(3)
        self.spinBoxD.setValue(self.pid.getKd())
        self.spinBoxD.setSingleStep(0.001)
        self.spinBoxD.setMinimum(.001)
        self.spinBoxD.setMaximum(9.999)

        self.sldChangeV = QSlider(Qt.Horizontal, self)

        self.canvas = MyMplCanvas(self, width=5, height=10, dpi=200)

        vbox = QVBoxLayout()
        vbox.addWidget(self.canvas)


        hbox = QHBoxLayout()

        self.groupboxPID = QGroupBox('PID', self)

        self.groupboxPID.setLayout(hbox)

        hbox.addWidget(self.labelP)
        hbox.addWidget(self.spinBoxP)
        hbox.addWidget(self.labelI)
        hbox.addWidget(self.spinBoxI)
        hbox.addWidget(self.labelD)
        hbox.addWidget(self.spinBoxD)

        # vbox.addLayout(hbox)

        vbox.addWidget(self.groupboxPID)


        vbox.addWidget(self.sldChangeV)
 

        self.setLayout(vbox)


        self.spinBoxP.valueChanged.connect(self.changeP)
        self.spinBoxI.valueChanged.connect(self.changeI)
        self.spinBoxD.valueChanged.connect(self.changeD)

        self.sldChangeV.valueChanged.connect(self.updateY)

        self.feedback=0.0

        self.x = np.linspace(0, 20, 100)
        self.p = 0.0
        self.y = self.x * 0
        if(isStatic):
            self.y[5:10]=2
            self.y[10:20]=6
            self.y[20:40]=3
        self.appendV=0
        self.line, = self.canvas.axes.plot(self.x, self.y, color='g', label='setPoint', animated=True, lw=1)

        self.pidX = np.linspace(0, 20, 100)
        self.pidP = 0.0
        self.pidY = self.pidX * 0
        self.pidAppendV=0
        self.pidLine, = self.canvas.axes.plot(self.pidX, self.pidY, 'r--', label='pid', animated=True, lw=1)

        self.canvas.axes.legend(loc="upper left", shadow=True)


        self.setGeometry(100, 100, 1000, 800)
        self.setWindowTitle('P I D 调节器')

        self.show()

    def changeP(self):
        pValue=self.spinBoxP.value()
        self.pid.setKp(pValue)

    def changeI(self):
        iValue=self.spinBoxI.value()
        self.pid.setKi(iValue)

    def changeD(self):
        dValue=self.spinBoxD.value()
        self.pid.setKd(dValue)

    def updateY(self, i):
        self.appendV=i/10
        self.y=self.y[1:]
        self.y=np.append(self.y, self.appendV)

    def update_line(self, i):

        if(isStatic):
            END=len(self.y)
            for index in range(1, END):
                self.pid.SetPoint = self.y[index]
                self.pid.update(self.feedback)
                output = self.pid.output
                if(index!=0):
                    self.feedback += (output - (1/index))
                self.pidY[index]=self.feedback
                time.sleep(0.011)

            self.line.set_ydata(self.y)
            self.pidLine.set_ydata(self.pidY)

            return [self.line, self.pidLine]
        
        else:
            self.pid.SetPoint = self.appendV
            self.pid.update(self.feedback)
            output = self.pid.output
            if(i!=0):
                self.feedback += (output - (1/i))
                # print('i:', i)
                # print('output - (1/i):', output - (1/i))
            self.pidAppendV=self.feedback
            
            self.y=self.y[1:]
            self.y=np.append(self.y, self.appendV)
            self.line.set_ydata(self.y)

            self.pidY=self.pidY[1:]
            self.pidY=np.append(self.pidY, self.pidAppendV)
            self.pidLine.set_ydata(self.pidY)

            return [self.line, self.pidLine]

    # def on_start(self):
    #     self.ani = FuncAnimation(self.canvas.figure, self.update_line, 
    #                              blit=True, interval=25)

    # def on_stop(self):
    #     self.ani._stop()


if __name__ == '__main__':

    # pid = PID.PID(0.65, 0.08, 0.001)
    pid = PID.PID(0.91, 0.01, 0.001)
    pid.SetPoint=0.0
    pid.setSampleTime(0.01)


    app = QApplication(sys.argv)
    ex = Example(pid)


    ani = FuncAnimation(ex.canvas.figure, ex.update_line, 
                                blit=True, interval=25)

    sys.exit(app.exec_())
    ```
    
    
    pid:
    
    ```py
    
    #!/usr/bin/python
#
# This file is part of IvPID.
# Copyright (C) 2015 Ivmech Mechatronics Ltd. <bilgi@ivmech.com>
#
# IvPID is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# IvPID is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

# title           :PID.py
# description     :python pid controller
# author          :Caner Durmusoglu
# date            :20151218
# version         :0.1
# notes           :
# python_version  :2.7
# ==============================================================================

"""Ivmech PID Controller is simple implementation of a Proportional-Integral-Derivative (PID) Controller in the Python Programming Language.
More information about PID Controller: http://en.wikipedia.org/wiki/PID_controller
"""
import time

class PID:
    """PID Controller
    """

    def __init__(self, P=0.2, I=0.0, D=0.0, current_time=None):

        self.Kp = P
        self.Ki = I
        self.Kd = D

        self.sample_time = 0.00
        self.current_time = current_time if current_time is not None else time.time()
        self.last_time = self.current_time

        self.clear()

    def clear(self):
        """Clears PID computations and coefficients"""
        self.SetPoint = 0.0

        self.PTerm = 0.0
        self.ITerm = 0.0
        self.DTerm = 0.0
        self.last_error = 0.0

        # Windup Guard
        self.int_error = 0.0
        self.windup_guard = 20.0

        self.output = 0.0

    def update(self, feedback_value, current_time=None):
        """Calculates PID value for given reference feedback

        .. math::
            u(t) = K_p e(t) + K_i \int_{0}^{t} e(t)dt + K_d {de}/{dt}

        .. figure:: images/pid_1.png
           :align:   center

           Test PID with Kp=1.2, Ki=1, Kd=0.001 (test_pid.py)

        """
        error = self.SetPoint - feedback_value

        self.current_time = current_time if current_time is not None else time.time()
        delta_time = self.current_time - self.last_time
        delta_error = error - self.last_error

        if (delta_time >= self.sample_time):
            self.PTerm = self.Kp * error
            self.ITerm += error * delta_time

            if (self.ITerm < -self.windup_guard):
                self.ITerm = -self.windup_guard
            elif (self.ITerm > self.windup_guard):
                self.ITerm = self.windup_guard

            self.DTerm = 0.0
            if delta_time > 0:
                self.DTerm = delta_error / delta_time

            # Remember last time and last error for next calculation
            self.last_time = self.current_time
            self.last_error = error

            self.output = self.PTerm + (self.Ki * self.ITerm) + (self.Kd * self.DTerm)

    def setKp(self, proportional_gain):
        """Determines how aggressively the PID reacts to the current error with setting Proportional Gain"""
        self.Kp = proportional_gain

    def setKi(self, integral_gain):
        """Determines how aggressively the PID reacts to the current error with setting Integral Gain"""
        self.Ki = integral_gain

    def setKd(self, derivative_gain):
        """Determines how aggressively the PID reacts to the current error with setting Derivative Gain"""
        self.Kd = derivative_gain


    def getKp(self):
        return self.Kp

    def getKi(self):
        return self.Ki

    def getKd(self):
        return self.Kd

    def setWindup(self, windup):
        """Integral windup, also known as integrator windup or reset windup,
        refers to the situation in a PID feedback controller where
        a large change in setpoint occurs (say a positive change)
        and the integral terms accumulates a significant error
        during the rise (windup), thus overshooting and continuing
        to increase as this accumulated error is unwound
        (offset by errors in the other direction).
        The specific problem is the excess overshooting.
        """
        self.windup_guard = windup

    def setSampleTime(self, sample_time):
        """PID that should be updated at a regular interval.
        Based on a pre-determined sampe time, the PID decides if it should compute or return immediately.
        """
        self.sample_time = sample_time
```

