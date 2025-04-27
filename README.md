# Westwagon
import React, { useState } from 'react';
import { View, Text, TextInput, Button, FlatList, TouchableOpacity, StyleSheet } from 'react-native';

export default function App() {
  const [userType, setUserType] = useState('user'); // 'user' or 'driver'
  const [address, setAddress] = useState('');
  const [notes, setNotes] = useState('');
  const [pickups, setPickups] = useState([
    { id: '1', address: '123 Main Street, Burlington', notes: '4 garbage bags', status: 'Requested' },
    { id: '2', address: '456 Maple Ave, Burlington', notes: 'Old sofa', status: 'Requested' }
  ]);

  const requestPickup = () => {
    if (address.trim() === '') return;
    const newPickup = {
      id: Date.now().toString(),
      address,
      notes,
      status: 'Requested',
    };
    setPickups([...pickups, newPickup]);
    setAddress('');
    setNotes('');
  };

  const acceptPickup = (id) => {
    setPickups(
      pickups.map(p => p.id === id ? { ...p, status: 'Accepted' } : p)
    );
  };

  const completePickup = (id) => {
    setPickups(
      pickups.map(p => p.id === id ? { ...p, status: 'Completed' } : p)
    );
  };

  const renderPickup = ({ item }) => (
    <View style={styles.pickupCard}>
      <Text style={styles.pickupText}>Address: {item.address}</Text>
      <Text style={styles.pickupText}>Notes: {item.notes}</Text>
      <Text style={styles.pickupText}>Status: {item.status}</Text>
      {userType === 'driver' && item.status === 'Requested' && (
        <Button title="Accept Pickup" onPress={() => acceptPickup(item.id)} />
      )}
      {userType === 'driver' && item.status === 'Accepted' && (
        <Button title="Complete Pickup" onPress={() => completePickup(item.id)} />
      )}
    </View>
  );

  return (
    <View style={styles.container}>
      <Text style={styles.header}>WasteWagon 🚛</Text>

      {/* User/Driver Switch */}
      <View style={styles.switchContainer}>
        <TouchableOpacity
          style={[styles.switchButton, userType === 'user' && styles.activeButton]}
          onPress={() => setUserType('user')}
        >
          <Text style={styles.switchText}>User</Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={[styles.switchButton, userType === 'driver' && styles.activeButton]}
          onPress={() => setUserType('driver')}
        >
          <Text style={styles.switchText}>Driver</Text>
        </TouchableOpacity>
      </View>

      {/* User View */}
      {userType === 'user' && (
        <View style={styles.formContainer}>
          <TextInput
            placeholder="Pickup Address"
            value={address}
            onChangeText={setAddress}
            style={styles.input}
          />
          <TextInput
            placeholder="Notes (optional)"
            value={notes}
            onChangeText={setNotes}
            style={styles.input}
          />
          <Button title="Request Pickup" onPress={requestPickup} />
        </View>
      )}

      {/* Pickup List */}
      <FlatList
        data={pickups}
        keyExtractor={item => item.id}
        renderItem={renderPickup}
        contentContainerStyle={{ paddingBottom: 100 }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 20, backgroundColor: '#fff' },
  header: { fontSize: 28, fontWeight: 'bold', textAlign: 'center', marginVertical: 20 },
  switchContainer: { flexDirection: 'row', marginBottom: 20, justifyContent: 'center' },
  switchButton: { padding: 10, borderWidth: 1, borderColor: '#ccc', marginHorizontal: 10, borderRadius: 5 },
  activeButton: { backgroundColor: '#4CAF50', borderColor: '#4CAF50' },
  switchText: { color: '#000' },
  formContainer: { marginBottom: 20 },
  input: { borderWidth: 1, borderColor: '#ccc', padding: 10, marginBottom: 10, borderRadius: 5 },
  pickupCard: { backgroundColor: '#f9f9f9', padding: 15, marginBottom: 10, borderRadius: 10 },
  pickupText: { marginBottom: 5 },
});
