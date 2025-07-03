# kayesoriano4.github.io
Family Health Dashboard
import React, { useState, useEffect } from 'react';
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card';
import { Plus, TrendingUp, TrendingDown, Heart, Moon, Activity, User, Calendar, AlertTriangle, CheckCircle } from 'lucide-react';

const FamilyHealthDashboard = () => {
  const [familyMembers, setFamilyMembers] = useState([]);
  const [selectedMember, setSelectedMember] = useState(null);
  const [showAddMember, setShowAddMember] = useState(false);
  const [showAddData, setShowAddData] = useState(false);
  const [showEditData, setShowEditData] = useState(false);
  const [editingEntry, setEditingEntry] = useState(null);
  const [selectedDate, setSelectedDate] = useState(new Date().toISOString().split('T')[0]);

  // Initialize with empty data
  useEffect(() => {
    setFamilyMembers([]);
  }, []);

  const [newMember, setNewMember] = useState({
    name: '',
    age: '',
    role: ''
  });

  const [newData, setNewData] = useState({
    recovery: '',
    sleepDuration: '',
    sleepQuality: '',
    deepSleep: '',
    remSleep: '',
    hrv: '',
    restingHr: '',
    strain: '',
    elevatedHrTime: ''
  });

  const [editData, setEditData] = useState({
    recovery: '',
    sleepDuration: '',
    sleepQuality: '',
    deepSleep: '',
    remSleep: '',
    hrv: '',
    restingHr: '',
    strain: '',
    elevatedHrTime: ''
  });

  const addFamilyMember = () => {
    if (newMember.name && newMember.age) {
      const member = {
        id: Date.now(),
        name: newMember.name,
        age: parseInt(newMember.age),
        role: newMember.role || 'Family Member',
        data: []
      };
      setFamilyMembers([...familyMembers, member]);
      setNewMember({ name: '', age: '', role: '' });
      setShowAddMember(false);
    }
  };

  const addHealthData = () => {
    if (selectedMember && Object.values(newData).some(val => val !== '')) {
      const updatedMembers = familyMembers.map(member => {
        if (member.id === selectedMember.id) {
          const existingDataIndex = member.data.findIndex(d => d.date === selectedDate);
          const dataEntry = {
            date: selectedDate,
            recovery: parseFloat(newData.recovery) || 0,
            sleepDuration: parseFloat(newData.sleepDuration) || 0,
            sleepQuality: parseFloat(newData.sleepQuality) || 0,
            deepSleep: parseFloat(newData.deepSleep) || 0,
            remSleep: parseFloat(newData.remSleep) || 0,
            hrv: parseFloat(newData.hrv) || 0,
            restingHr: parseFloat(newData.restingHr) || 0,
            strain: parseFloat(newData.strain) || 0,
            elevatedHrTime: parseFloat(newData.elevatedHrTime) || 0
          };
          
          if (existingDataIndex >= 0) {
            member.data[existingDataIndex] = dataEntry;
          } else {
            member.data.push(dataEntry);
          }
          member.data.sort((a, b) => new Date(b.date) - new Date(a.date));
        }
        return member;
      });
      
      setFamilyMembers(updatedMembers);
      setNewData({
        recovery: '', sleepDuration: '', sleepQuality: '', deepSleep: '', remSleep: '',
        hrv: '', restingHr: '', strain: '', elevatedHrTime: ''
      });
      setShowAddData(false);
    }
  };

  const editHealthData = () => {
    if (selectedMember && editingEntry) {
      const updatedMembers = familyMembers.map(member => {
        if (member.id === selectedMember.id) {
          const updatedData = member.data.map(entry => {
            if (entry.date === editingEntry.date) {
              return {
                ...entry,
                recovery: parseFloat(editData.recovery) || entry.recovery,
                sleepDuration: parseFloat(editData.sleepDuration) || entry.sleepDuration,
                sleepQuality: parseFloat(editData.sleepQuality) || entry.sleepQuality,
                deepSleep: parseFloat(editData.deepSleep) || entry.deepSleep,
                remSleep: parseFloat(editData.remSleep) || entry.remSleep,
                hrv: parseFloat(editData.hrv) || entry.hrv,
                restingHr: parseFloat(editData.restingHr) || entry.restingHr,
                strain: parseFloat(editData.strain) || entry.strain,
                elevatedHrTime: parseFloat(editData.elevatedHrTime) || entry.elevatedHrTime
              };
            }
            return entry;
          });
          return { ...member, data: updatedData };
        }
        return member;
      });
      
      setFamilyMembers(updatedMembers);
      setEditData({
        recovery: '', sleepDuration: '', sleepQuality: '', deepSleep: '', remSleep: '',
        hrv: '', restingHr: '', strain: '', elevatedHrTime: ''
      });
      setShowEditData(false);
      setEditingEntry(null);
    }
  };

  const startEdit = (member, dataEntry) => {
    setSelectedMember(member);
    setEditingEntry(dataEntry);
    setEditData({
      recovery: dataEntry.recovery.toString(),
      sleepDuration: dataEntry.sleepDuration.toString(),
      sleepQuality: dataEntry.sleepQuality.toString(),
      deepSleep: dataEntry.deepSleep.toString(),
      remSleep: dataEntry.remSleep.toString(),
      hrv: dataEntry.hrv.toString(),
      restingHr: dataEntry.restingHr.toString(),
      strain: dataEntry.strain.toString(),
      elevatedHrTime: dataEntry.elevatedHrTime.toString()
    });
    setShowEditData(true);
  };

  const deleteHealthData = (member, dataEntry) => {
    const updatedMembers = familyMembers.map(m => {
      if (m.id === member.id) {
        return {
          ...m,
          data: m.data.filter(entry => entry.date !== dataEntry.date)
        };
      }
      return m;
    });
    setFamilyMembers(updatedMembers);
  };

  const getHealthStatus = (member) => {
    if (!member.data.length) return { status: 'No data', color: 'text-gray-500' };
    
    const latest = member.data[0];
    const recovery = latest.recovery;
    const sleepQuality = latest.sleepQuality;
    const hrv = latest.hrv;
    
    if (recovery < 50 || sleepQuality < 60 || hrv < 25) {
      return { status: 'Needs attention', color: 'text-red-500' };
    } else if (recovery < 70 || sleepQuality < 75 || hrv < 35) {
      return { status: 'Monitor closely', color: 'text-yellow-500' };
    } else {
      return { status: 'Good', color: 'text-green-500' };
    }
  };

  const getTrend = (member, metric) => {
    if (member.data.length < 2) return null;
    const latest = member.data[0][metric];
    const previous = member.data[1][metric];
    return latest > previous ? 'up' : latest < previous ? 'down' : 'stable';
  };

  const formatDate = (dateString) => {
    return new Date(dateString).toLocaleDateString('en-US', {
      weekday: 'short',
      month: 'short',
      day: 'numeric'
    });
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-4">
      <div className="max-w-7xl mx-auto">
        {/* Header */}
        <div className="mb-8">
          <div className="flex justify-between items-center mb-4">
            <div>
              <h1 className="text-3xl font-bold text-gray-900">Family Health Dashboard</h1>
              <p className="text-gray-600">Monitor daily wellness metrics for your family</p>
            </div>
            <div className="flex gap-2">
              <button
                onClick={() => setShowAddMember(true)}
                className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 flex items-center gap-2"
              >
                <Plus className="w-4 h-4" />
                Add Member
              </button>
            </div>
          </div>
          
          <div className="bg-white rounded-lg shadow-sm p-4 mb-6">
            <div className="flex items-center justify-between">
              <div className="flex items-center gap-2 text-sm text-gray-600">
                <Calendar className="w-4 h-4" />
                Today: {formatDate(selectedDate)}
              </div>
              <div className="flex items-center gap-2 text-sm text-orange-600 bg-orange-50 px-3 py-1 rounded-full">
                <AlertTriangle className="w-4 h-4" />
                Data saved for current session only
              </div>
            </div>
          </div>
        </div>

        {/* Family Members Grid */}
        <div className="grid grid-cols-1 lg:grid-cols-2 xl:grid-cols-3 gap-6 mb-8">
          {familyMembers.map(member => {
            const healthStatus = getHealthStatus(member);
            const latestData = member.data[0];
            
            return (
              <Card key={member.id} className="hover:shadow-lg transition-shadow">
                <CardHeader className="pb-3">
                  <div className="flex justify-between items-start">
                    <div className="flex items-center gap-3">
                      <div className="w-12 h-12 bg-gradient-to-br from-blue-500 to-purple-600 rounded-full flex items-center justify-center text-white font-bold">
                        {member.name.charAt(0)}
                      </div>
                      <div>
                        <CardTitle className="text-lg">{member.name}</CardTitle>
                        <CardDescription>{member.role}, {member.age} years</CardDescription>
                      </div>
                    </div>
                    <div className="flex items-center gap-2">
                      <span className={`text-sm font-medium ${healthStatus.color}`}>
                        {healthStatus.status}
                      </span>
                      {healthStatus.status === 'Good' && <CheckCircle className="w-4 h-4 text-green-500" />}
                      {healthStatus.status === 'Needs attention' && <AlertTriangle className="w-4 h-4 text-red-500" />}
                    </div>
                  </div>
                </CardHeader>
                
                <CardContent>
                  {latestData ? (
                    <div className="space-y-4">
                      <div className="grid grid-cols-2 gap-4">
                        <div className="bg-gradient-to-r from-green-50 to-green-100 p-3 rounded-lg">
                          <div className="flex items-center gap-2 mb-1">
                            <Activity className="w-4 h-4 text-green-600" />
                            <span className="text-sm text-green-700">Recovery</span>
                            {getTrend(member, 'recovery') === 'up' && <TrendingUp className="w-3 h-3 text-green-600" />}
                            {getTrend(member, 'recovery') === 'down' && <TrendingDown className="w-3 h-3 text-red-600" />}
                          </div>
                          <div className="text-2xl font-bold text-green-800">{latestData.recovery}%</div>
                        </div>
                        
                        <div className="bg-gradient-to-r from-blue-50 to-blue-100 p-3 rounded-lg">
                          <div className="flex items-center gap-2 mb-1">
                            <Moon className="w-4 h-4 text-blue-600" />
                            <span className="text-sm text-blue-700">Sleep</span>
                            {getTrend(member, 'sleepDuration') === 'up' && <TrendingUp className="w-3 h-3 text-green-600" />}
                            {getTrend(member, 'sleepDuration') === 'down' && <TrendingDown className="w-3 h-3 text-red-600" />}
                          </div>
                          <div className="text-2xl font-bold text-blue-800">{latestData.sleepDuration}h</div>
                        </div>
                      </div>
                      
                      <div className="grid grid-cols-2 gap-4 text-sm">
                        <div>
                          <span className="text-gray-600">Sleep Quality:</span>
                          <span className="font-semibold ml-2">{latestData.sleepQuality}%</span>
                        </div>
                        <div>
                          <span className="text-gray-600">HRV:</span>
                          <span className="font-semibold ml-2">{latestData.hrv}ms</span>
                        </div>
                        <div>
                          <span className="text-gray-600">Resting HR:</span>
                          <span className="font-semibold ml-2">{latestData.restingHr} bpm</span>
                        </div>
                        <div>
                          <span className="text-gray-600">Strain:</span>
                          <span className="font-semibold ml-2">{latestData.strain}</span>
                        </div>
                      </div>
                      
                      <div className="text-xs text-gray-500 border-t pt-2 flex justify-between items-center">
                        <span>Last updated: {formatDate(latestData.date)}</span>
                        <div className="flex gap-1">
                          <button
                            onClick={() => startEdit(member, latestData)}
                            className="text-blue-600 hover:text-blue-800 text-xs underline"
                          >
                            Edit
                          </button>
                          <button
                            onClick={() => deleteHealthData(member, latestData)}
                            className="text-red-600 hover:text-red-800 text-xs underline"
                          >
                            Delete
                          </button>
                        </div>
                      </div>
                    </div>
                  ) : (
                    <div className="text-center py-8 text-gray-500">
                      <User className="w-12 h-12 mx-auto mb-2 opacity-50" />
                      <p>No data available</p>
                    </div>
                  )}
                  
                  <div className="flex gap-2 mt-4">
                    <button
                      onClick={() => {
                        setSelectedMember(member);
                        setShowAddData(true);
                      }}
                      className="flex-1 bg-blue-50 text-blue-600 py-2 rounded-lg hover:bg-blue-100 transition-colors"
                    >
                      Add Data
                    </button>
                    {member.data.length > 0 && (
                      <button
                        onClick={() => startEdit(member, member.data[0])}
                        className="flex-1 bg-green-50 text-green-600 py-2 rounded-lg hover:bg-green-100 transition-colors"
                      >
                        Edit Latest
                      </button>
                    )}
                  </div>
                </CardContent>
              </Card>
            );
          })}
        </div>

        {/* Add Member Modal */}
        {showAddMember && (
          <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div className="bg-white rounded-lg p-6 w-full max-w-md">
              <h2 className="text-xl font-bold mb-4">Add Family Member</h2>
              <div className="space-y-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Name</label>
                  <input
                    type="text"
                    value={newMember.name}
                    onChange={(e) => setNewMember({...newMember, name: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="Enter name"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Age</label>
                  <input
                    type="number"
                    value={newMember.age}
                    onChange={(e) => setNewMember({...newMember, age: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="Enter age"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Role</label>
                  <input
                    type="text"
                    value={newMember.role}
                    onChange={(e) => setNewMember({...newMember, role: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="e.g., Father, Mother, Child"
                  />
                </div>
              </div>
              <div className="flex gap-2 mt-6">
                <button
                  onClick={addFamilyMember}
                  className="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700"
                >
                  Add Member
                </button>
                <button
                  onClick={() => setShowAddMember(false)}
                  className="flex-1 bg-gray-200 text-gray-700 py-2 rounded-lg hover:bg-gray-300"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
        )}

        {/* Add Data Modal */}
        {showAddData && selectedMember && (
          <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div className="bg-white rounded-lg p-6 w-full max-w-2xl max-h-[90vh] overflow-y-auto">
              <h2 className="text-xl font-bold mb-4">
                Add Health Data for {selectedMember.name}
              </h2>
              
              <div className="mb-4">
                <label className="block text-sm font-medium text-gray-700 mb-1">Date</label>
                <input
                  type="date"
                  value={selectedDate}
                  onChange={(e) => setSelectedDate(e.target.value)}
                  className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                />
              </div>
              
              <div className="grid grid-cols-2 gap-4 mb-6">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Recovery (%)</label>
                  <input
                    type="number"
                    value={newData.recovery}
                    onChange={(e) => setNewData({...newData, recovery: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0-100"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Sleep Duration (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={newData.sleepDuration}
                    onChange={(e) => setNewData({...newData, sleepDuration: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Sleep Quality (%)</label>
                  <input
                    type="number"
                    value={newData.sleepQuality}
                    onChange={(e) => setNewData({...newData, sleepQuality: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0-100"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Deep Sleep (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={newData.deepSleep}
                    onChange={(e) => setNewData({...newData, deepSleep: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">REM Sleep (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={newData.remSleep}
                    onChange={(e) => setNewData({...newData, remSleep: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">HRV (ms)</label>
                  <input
                    type="number"
                    value={newData.hrv}
                    onChange={(e) => setNewData({...newData, hrv: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Resting HR (bpm)</label>
                  <input
                    type="number"
                    value={newData.restingHr}
                    onChange={(e) => setNewData({...newData, restingHr: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Strain</label>
                  <input
                    type="number"
                    step="0.1"
                    value={newData.strain}
                    onChange={(e) => setNewData({...newData, strain: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Elevated HR Time (min)</label>
                  <input
                    type="number"
                    value={newData.elevatedHrTime}
                    onChange={(e) => setNewData({...newData, elevatedHrTime: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
              </div>
              
              <div className="flex gap-2">
                <button
                  onClick={addHealthData}
                  className="flex-1 bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700"
                >
                  Save Data
                </button>
                <button
                  onClick={() => setShowAddData(false)}
                  className="flex-1 bg-gray-200 text-gray-700 py-2 rounded-lg hover:bg-gray-300"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
        )}
        {/* Edit Data Modal */}
        {showEditData && selectedMember && editingEntry && (
          <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div className="bg-white rounded-lg p-6 w-full max-w-2xl max-h-[90vh] overflow-y-auto">
              <h2 className="text-xl font-bold mb-4">
                Edit Health Data for {selectedMember.name}
              </h2>
              
              <div className="mb-4 p-3 bg-blue-50 rounded-lg">
                <div className="text-sm text-blue-700">
                  Editing data for: <span className="font-semibold">{formatDate(editingEntry.date)}</span>
                </div>
              </div>
              
              <div className="grid grid-cols-2 gap-4 mb-6">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Recovery (%)</label>
                  <input
                    type="number"
                    value={editData.recovery}
                    onChange={(e) => setEditData({...editData, recovery: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0-100"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Sleep Duration (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={editData.sleepDuration}
                    onChange={(e) => setEditData({...editData, sleepDuration: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Sleep Quality (%)</label>
                  <input
                    type="number"
                    value={editData.sleepQuality}
                    onChange={(e) => setEditData({...editData, sleepQuality: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0-100"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Deep Sleep (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={editData.deepSleep}
                    onChange={(e) => setEditData({...editData, deepSleep: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">REM Sleep (hours)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={editData.remSleep}
                    onChange={(e) => setEditData({...editData, remSleep: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">HRV (ms)</label>
                  <input
                    type="number"
                    value={editData.hrv}
                    onChange={(e) => setEditData({...editData, hrv: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Resting HR (bpm)</label>
                  <input
                    type="number"
                    value={editData.restingHr}
                    onChange={(e) => setEditData({...editData, restingHr: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Strain</label>
                  <input
                    type="number"
                    step="0.1"
                    value={editData.strain}
                    onChange={(e) => setEditData({...editData, strain: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-1">Elevated HR Time (min)</label>
                  <input
                    type="number"
                    value={editData.elevatedHrTime}
                    onChange={(e) => setEditData({...editData, elevatedHrTime: e.target.value})}
                    className="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    placeholder="0"
                  />
                </div>
              </div>
              
              <div className="flex gap-2">
                <button
                  onClick={editHealthData}
                  className="flex-1 bg-green-600 text-white py-2 rounded-lg hover:bg-green-700"
                >
                  Update Data
                </button>
                <button
                  onClick={() => {
                    setShowEditData(false);
                    setEditingEntry(null);
                  }}
                  className="flex-1 bg-gray-200 text-gray-700 py-2 rounded-lg hover:bg-gray-300"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default FamilyHealthDashboard;
